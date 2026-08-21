<template>
  <div class="tile">
    <img :src="$props.image ? $props.image : 'data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw=='" />
    <h3>{{ $props.name }}</h3>
    <p>{{ $props.description }}</p>
    <div class="actions" v-if="$props.buttons && $props.buttons.length > 0">
      <a v-for="button in props.buttons" :key="button.link" :class="'btn-' + button.type" :href="button.link">{{ button.label }}</a>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Button {
  label: string
  link: string
  type: string
}

const props = withDefaults(
  defineProps<{
    image?: string
    name?: string
    description?: string
    buttons?: Button[]
  }>(),
  {
    image: 'data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==',
    name: 'Project title',
    description: 'Project description',
    buttons: () => [],
  },
)
</script>

<style lang="scss" scoped>
.tile {
  display: flex;
  position: relative;
  flex-direction: column;
  justify-content: space-between;
  align-items: stretch;
  gap: $gap-sm;
  backdrop-filter: blur(10px) saturate(1.6);
  outline: 1px solid $divider;
  background: rgb(255 255 255 / 1%);
  isolation: isolate;
  padding: $gap-md;
  overflow: hidden;

  &::after {
    position: absolute;
    opacity: 0.1;
    mix-blend-mode: soft-light;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='300' height='300'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.80' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
    background-size: 300px 300px;
    pointer-events: none;
    content: '';
  }

  img {
    aspect-ratio: 16 / 10;
    width: 100%;
    object-fit: cover;
  }

  h3 {
    margin: 0.4rem 0 0;
    color: $text-primary;
    font-weight: 400;
    font-size: 2.2rem;
    line-height: 1.2;
    font-family: $font-accent;
  }

  p {
    color: $text-secondary;
    font-size: 1.4rem;
    line-height: 1.65;
    font-family: $font-body;
    letter-spacing: -0.01em;
  }

  .actions {
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: 2rem;
    margin-top: auto;
    width: 100%;
    height: auto;

    .btn-primary {
      padding: 1rem 2rem;
      line-height: 1em;
    }

    .btn-secondary {
      &::after {
        position: absolute;
        top: -1rem;
        right: -2rem;
        bottom: -1rem;
        left: -2rem;
        content: '';
      }
    }
  }
}
</style>
