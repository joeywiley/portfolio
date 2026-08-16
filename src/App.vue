<template>
  <ParticleCanvas ref="canvasRef" class="particle-canvas" />
  <div class="content-wrapper" @mousedown.self="handleMouseDown" @mouseup="handleMouseUp">
    <SiteHeader />
    <section id="title" ref="titleRef" class="title-page">
      <TitlePage />
    </section>
    <section id="content">
      <WorkSection />
      <AboutSection />
      <ContactSection />
    </section>
    <Transition name="fade">
      <ScrollButton class="top-button" icon="keyboard_arrow_up" label="TOP" section="title" v-if="showTopButton" />
    </Transition>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

import ParticleCanvas from '@/components/ParticleCanvas.vue'
import SiteHeader from '@/components/SiteHeader.vue'
import TitlePage from '@/components/section/title/TitlePage.vue'
import WorkSection from '@/components/section/work/WorkSection.vue'
import AboutSection from '@/components/section/about/AboutSection.vue'
import ContactSection from '@/components/section/contact/ContactSection.vue'
import ScrollButton from '@/components/common/ScrollButton.vue'

const canvasRef = ref<InstanceType<typeof ParticleCanvas> | null>(null)
const titleRef = ref<HTMLElement | null>(null)
const showTopButton = ref(false)

function handleScroll() {
  canvasRef.value!.handleScroll()
  showTopButton.value = document.documentElement.scrollTop > titleRef.value!.offsetHeight * 0.9
}

function handleMouseDown(event: Event) {
  event.preventDefault()
  canvasRef.value!.handleMouseDown()
}

function handleMouseUp() {
  canvasRef.value!.handleMouseUp()
}

function handleAnchorClick(event: Event) {
  const target = event.target as HTMLElement
  const link = target.closest('a[href^="#"]')
  if (!link) return

  event.preventDefault()
  const id = link.getAttribute('href')!.slice(1)
  const el = document.getElementById(id)
  if (el) {
    el.scrollIntoView({ behavior: 'smooth' })
  }
}

onMounted(() => {
  document.addEventListener('click', handleAnchorClick)
  document.addEventListener('scroll', handleScroll)
})
onUnmounted(() => {
  document.removeEventListener('click', handleAnchorClick)
  document.removeEventListener('scroll', handleScroll)
})
</script>

<style lang="scss" scoped>
.particle-canvas {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: -10;
}

.content-wrapper {
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  width: 100%;
  height: auto;
  touch-action: pan-y;
  pointer-events: auto;
}

#title,
#content {
  scroll-snap-align: start;
}

.title-page {
  height: 100lvh;
}

.top-button {
  position: fixed;
  right: 48px;
  bottom: 24px;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
