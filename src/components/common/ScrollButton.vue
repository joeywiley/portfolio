<template>
  <a :href="'#' + props.section" class="scroll-hint btn-quaternary">
    <span class="material-symbols-rounded">{{ props.icon }}</span>
    <span>{{ props.label }}</span>
  </a>
</template>

<script lang="ts" setup>
const props = defineProps({
  icon: String,
  label: String,
  section: String,
})
</script>

<style lang="scss" scoped>
.scroll-hint {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 1.1rem;
  font-family: $font-mono;
  letter-spacing: 0.1em;
}
</style>
