<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const canvasRef = ref<HTMLCanvasElement | null>(null)

const DENSITY = 1

const INITIAL_SPEED_MULTIPLIER = 0.0003
const SPEED_MULTIPLIER = 0.3
const MIN_FRICTION = 0.9
const MAX_FRICTION = 0.75

const PULL_STRENGTH = 0.04
const PULL_RADIUS = 500
const INTERACT_MULTIPLIER = 4
const SCROLL_STRENGTH = 0.0001

let width: number
let height: number

let mouse_x_prev = 0
let mouse_y_prev = 0
let mouse_x = 0
let mouse_y = 0
let mouse_down = false
let mouse_init = false

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
}

let particles: Particle[] = []
let ctx: CanvasRenderingContext2D | null = null

function createParticles(quantity: number): Particle[] {
  const particles: Particle[] = []
  for (let i = 0; i < quantity; i++) {
    const z = Math.random()
    const dx = (Math.random() - 0.5) * INITIAL_SPEED_MULTIPLIER * (1 - z)
    const dy = (Math.random() - 0.5) * INITIAL_SPEED_MULTIPLIER * (1 - z)

    particles.push({
      x: Math.random(),
      y: Math.random(),
      z: z,
      dx: dx,
      dy: dy,
      min_speed: Math.sqrt(dx ** 2 + dy ** 2),
    })
  }
  return particles
}

function updateParticles() {
  const mouse_vel_x = mouse_init ? mouse_x - mouse_x_prev : 0
  const mouse_vel_y = mouse_init ? mouse_y - mouse_y_prev : 0
  mouse_x_prev = mouse_x
  mouse_y_prev = mouse_y

  for (const particle of particles) {
    if (mouse_init) {
      const px = particle.x * width
      const py = particle.y * height
      const distance = Math.sqrt((px - mouse_x) ** 2 + (py - mouse_y) ** 2)
      const distance_scale = 1 - scale(distance, 0, PULL_RADIUS, 0, 1) // far particles respond less
      const depth_resistance = 1 - particle.z // deep particles respond less
      const mouse_down_multiplier = mouse_down ? INTERACT_MULTIPLIER : 1

      particle.dx += (mouse_vel_x / width) * distance_scale * depth_resistance * PULL_STRENGTH * mouse_down_multiplier
      particle.dy += (mouse_vel_y / height) * distance_scale * depth_resistance * PULL_STRENGTH * mouse_down_multiplier
    }

    const friction = MIN_FRICTION + (MAX_FRICTION - MIN_FRICTION) * particle.z
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

    particle.x += particle.dx * SPEED_MULTIPLIER
    if (particle.x > 1) particle.x = 0
    else if (particle.x < 0) particle.x = 1

    particle.y += particle.dy * SPEED_MULTIPLIER
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
  mouse_init = true
}

function handleMouseDown() {
  mouse_down = true
}

function handleMouseUp() {
  mouse_down = false
}

function handleTouchMove(event: TouchEvent) {
  const touch = event.touches[0]
  if (!touch) return

  const canvas = canvasRef!.value!
  const rect = canvas.getBoundingClientRect()!
  mouse_x = touch.clientX - rect.left
  mouse_y = touch.clientY - rect.top
  mouse_init = true
}

function handleScroll(el: HTMLDivElement) {
  if (!scroll_init) {
    scroll_pos = el.scrollTop
    scroll_init = true
    return
  }

  scroll_pos_prev = scroll_pos
  scroll_pos = el.scrollTop
  const scroll_adjustment = (scroll_pos_prev - scroll_pos) * SCROLL_STRENGTH

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

  width = document.body.clientWidth
  height = document.body.clientHeight
  const dpr = window.devicePixelRatio || 1
  const num_particles = width * height * (DENSITY / 10000)

  canvas.width = width * dpr
  canvas.height = height * dpr
  canvas.style.width = `${width}px`
  canvas.style.height = `${height}px`

  ctx = canvas.getContext('2d', { alpha: false })!
  ctx.scale(dpr, dpr)

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
  for (const particle of particles) {
    ctx!.fillStyle = `hsl(1, 0%, ${(1 - particle.z) * 100}%)`
    ctx!.fillRect(particle.x * width, particle.y * height, 4, 4)
  }
}

onMounted(() => {
  resizeCanvas()
  animate()
  window.addEventListener('resize', resizeCanvas)
  window.addEventListener('mousemove', handleMouseMove)
  window.addEventListener('touchmove', handleTouchMove, { passive: true })
})

onUnmounted(() => {
  cancelAnimationFrame(current_animation_frame)
  window.removeEventListener('resize', resizeCanvas)
  window.removeEventListener('mousemove', handleMouseMove)
  window.removeEventListener('touchmove', handleTouchMove)
})

defineExpose({ handleScroll, handleMouseDown, handleMouseUp })
</script>

<template>
  <canvas ref="canvasRef"></canvas>
</template>

<style lang="scss" scoped></style>
