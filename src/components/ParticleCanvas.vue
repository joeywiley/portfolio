<template>
  <canvas ref="canvasRef"></canvas>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const canvasRef = ref<HTMLCanvasElement | null>(null)

const PARTICLE_CONFIG = {
  particle_density: 1, // per 10,000 px^2
  size: 4, // px
  colors: [
    { hex: '#ffffff', weight: 85 },
    { hex: '#ffddcc', weight: 5 },
    { hex: '#cceeff', weight: 5 },
    { hex: '#ebe5ff', weight: 5 },
  ],
} as const

const DEPTH_PHYSICS = {
  base_speed_near: 0.00005,
  base_speed_far: 0,
  friction_near: 0.9,
  friction_far: 0.75,
} as const

const INTERACTION_CONFIG = {
  pull_strength: 0.01,
  pull_radius: 500, // px
  drag_strength_mul: 4,
  scroll_strength: 0.00005,
} as const

let resize_observer: ResizeObserver | null = null

let width: number
let height: number

let mouse_x_prev = 0
let mouse_y_prev = 0
let mouse_x = 0
let mouse_y = 0
let mouse_down = false

let scroll_pos = 0
let scroll_pos_prev = 0
let scroll_init = false

interface Particle {
  x: number
  y: number
  z: number
  dx: number
  dy: number
  min_speed: number
  color: string
}

let particles: Particle[] = []
let ctx: CanvasRenderingContext2D | null = null

function pickWeightedColor() {
  const total = PARTICLE_CONFIG.colors.reduce((sum, color) => sum + color.weight, 0)
  let roll = Math.random() * total

  for (const color of PARTICLE_CONFIG.colors) {
    roll -= color.weight
    if (roll <= 0) return color.hex
  }

  return PARTICLE_CONFIG.colors[PARTICLE_CONFIG.colors.length - 1]!.hex
}

function createParticles(quantity: number): Particle[] {
  const particles: Particle[] = []
  const speedRange = (DEPTH_PHYSICS.base_speed_far - DEPTH_PHYSICS.base_speed_near) * (2000 / document.documentElement.clientWidth)

  for (let i = 0; i < quantity; i++) {
    const z = Math.random()
    const dx = (Math.random() - 0.5) * 2 * speedRange * (1 - z)
    const dy = (Math.random() - 0.5) * 2 * speedRange * (1 - z)

    particles.push({
      x: Math.random(),
      y: Math.random(),
      z: z,
      dx: dx,
      dy: dy,
      min_speed: Math.sqrt(dx ** 2 + dy ** 2),
      color: pickWeightedColor(),
    })
  }
  return particles
}

function updateParticles() {
  const mouse_vel_x = mouse_x - mouse_x_prev
  const mouse_vel_y = mouse_y - mouse_y_prev
  mouse_x_prev = mouse_x
  mouse_y_prev = mouse_y

  for (const particle of particles) {
    const px = particle.x * width
    const py = particle.y * height
    const distance = Math.sqrt((px - mouse_x) ** 2 + (py - mouse_y) ** 2)
    const distance_scale = 1 - scale(distance, 0, INTERACTION_CONFIG.pull_radius, 0, 1) // far particles respond less
    const depth_resistance = 1 - particle.z // deep particles respond less
    const mouse_down_multiplier = mouse_down ? INTERACTION_CONFIG.drag_strength_mul : 1

    particle.dx += (mouse_vel_x / width) * distance_scale * depth_resistance * INTERACTION_CONFIG.pull_strength * mouse_down_multiplier
    particle.dy += (mouse_vel_y / height) * distance_scale * depth_resistance * INTERACTION_CONFIG.pull_strength * mouse_down_multiplier

    const friction = DEPTH_PHYSICS.friction_near + (DEPTH_PHYSICS.friction_far - DEPTH_PHYSICS.friction_near) * particle.z
    const current_speed = Math.sqrt(particle.dx ** 2 + particle.dy ** 2)
    const adjusted_speed = current_speed * friction
    if (adjusted_speed <= particle.min_speed) {
      const scale = particle.min_speed / current_speed
      particle.dx *= scale
      particle.dy *= scale
    } else {
      particle.dx *= friction
      particle.dy *= friction
    }

    particle.x += particle.dx
    if (particle.x > 1) particle.x = 0
    else if (particle.x < 0) particle.x = 1

    particle.y += particle.dy
    if (particle.y > 1) particle.y = 0
    else if (particle.y < 0) particle.y = 1
  }

  draw()
}

function handleMouseMove(event: MouseEvent) {
  const canvas = canvasRef!.value!
  const rect = canvas.getBoundingClientRect()!
  mouse_x = event.clientX - rect.left
  mouse_y = event.clientY - rect.top

  if (Math.sqrt((mouse_x - mouse_x_prev) ** 2 + (mouse_y - mouse_y_prev) ** 2) > 100) {
    mouse_x_prev = mouse_x
    mouse_y_prev = mouse_y
  }
}

function handleMouseDown() {
  mouse_down = true
}

function handleMouseUp() {
  mouse_down = false
}

function handleScroll() {
  if (!scroll_init) {
    scroll_pos = document.documentElement.scrollTop
    scroll_init = true
    return
  }

  scroll_pos_prev = scroll_pos
  scroll_pos = document.documentElement.scrollTop
  const scroll_adjustment = (scroll_pos_prev - scroll_pos) * INTERACTION_CONFIG.scroll_strength

  for (const particle of particles) {
    particle.dy += scroll_adjustment
  }
}

let current_animation_frame: number

function animate() {
  updateParticles()
  current_animation_frame = requestAnimationFrame(animate)
}

const clamp = (num: number, min: number, max: number) => Math.min(Math.max(num, min), max)

const scale = (input: number, input_min: number, input_max: number, output_min: number, output_max: number): number => {
  const percent = (input - input_min) / (input_max - input_min)
  const output = percent * (output_max - output_min) + output_min

  return clamp(output, output_min, output_max)
}

function resizeCanvas() {
  const canvas = canvasRef.value!

  const rect = canvas.getBoundingClientRect()
  const dpr = window.devicePixelRatio || 1

  width = rect.width
  height = rect.height

  canvas.width = width * dpr
  canvas.height = height * dpr

  ctx = canvas.getContext('2d', { alpha: true })!
  ctx.scale(dpr, dpr)

  const num_particles = width * height * (PARTICLE_CONFIG.particle_density / 10000)
  if (particles.length == 0) {
    particles = createParticles(num_particles)
  } else if (particles.length > num_particles) {
    while (particles.length > num_particles) {
      particles.pop()
    }
  } else if (particles.length < num_particles) {
    while (particles.length < num_particles) {
      particles.push(createParticles(1)[0]!)
    }
  }

  draw()
}

function draw() {
  ctx!.clearRect(0, 0, width, height)
  ctx?.save()
  for (const particle of particles) {
    ctx!.fillStyle = particle.color
    ctx!.globalAlpha = 1 - particle.z
    ctx!.fillRect(particle.x * width, particle.y * height, PARTICLE_CONFIG.size, PARTICLE_CONFIG.size)
  }
  ctx?.restore()
}

onMounted(() => {
  resizeCanvas()
  animate()

  resize_observer = new ResizeObserver(() => resizeCanvas())
  resize_observer.observe(canvasRef.value!)

  window.addEventListener('mousemove', handleMouseMove)
})

onUnmounted(() => {
  cancelAnimationFrame(current_animation_frame)
  window.removeEventListener('mousemove', handleMouseMove)
})

defineExpose({ handleScroll, handleMouseDown, handleMouseUp })
</script>

<style lang="scss" scoped></style>
