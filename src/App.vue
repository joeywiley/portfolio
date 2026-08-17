<template>
  <div class="particle-canvas-wrapper">
    <ParticleCanvas ref="canvasRef" class="particle-canvas" />
  </div>
  <SiteHeader />
  <div class="content-wrapper" @mousedown.self="handleMouseDown" @mouseup="handleMouseUp">
    <section id="title" ref="titleRef" class="title-page">
      <TitlePage />
    </section>
    <section id="content">
      <WorkSection />
      <AboutSection />
      <ContactSection />
    </section>
  </div>
  <Transition name="fade">
    <ScrollButton class="top-button" icon="keyboard_arrow_up" label="TOP" section="title" v-if="showTopButton" />
  </Transition>
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

onMounted(() => {
  document.addEventListener('scroll', handleScroll)
})
onUnmounted(() => {
  document.removeEventListener('scroll', handleScroll)
})
</script>

<style lang="scss" scoped>
.particle-canvas-wrapper {
  position: fixed;
  z-index: -10;
  width: 100lvw;
  height: calc(100lvh + 60px); // to extend under safari search bar
}

.particle-canvas {
  z-index: -10;
  width: 100%;
  height: 100%;
}

.content-wrapper {
  padding-top: env(safe-area-inset-top);
  padding-bottom: env(safe-area-inset-bottom);
  width: 100%;
  height: auto;
  pointer-events: auto;
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
