<template>
  <section id="work" class="work-page">
    <h2>Work</h2>
    <div class="tiles">
      <ProjectTile />
      <ProjectTile />
      <ProjectTile />
    </div>
  </section>
</template>

<script lang="ts" setup>
import ProjectTile from './ProjectTile.vue'
</script>

<style lang="scss" scoped>
.work-page {
  padding-right: $gutters-h;
  padding-left: $gutters-h;
  width: 100%;
  height: 200lvh;

  h2 {
    margin: 0 0 $gap-lg;
    color: $text-primary;
    font-weight: 500;
    font-size: clamp(3.2rem, 5vw, 5.2rem);
    line-height: 1.05;
    font-family: $font-accent;
    letter-spacing: -0.01em;
    text-shadow: $shadow;
  }
}

.tiles {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(100%, 300px), 400px));
  gap: $gap-md;
}
</style>
