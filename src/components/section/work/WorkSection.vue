<template>
  <section id="work" class="work-page">
    <h2>Work</h2>
    <div class="tiles">
      <ProjectTile
        image="https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Flogosmarcas.net%2Fwp-content%2Fuploads%2F2020%2F09%2FGoogle-Emblema.png&f=1&nofb=1&ipt=3e3c81690a8c66ee60441fbcd28de3250b3041b2ad886a92575369d997cdabf3"
        name="Gooble"
        description="goober"
        :buttons="[
          { type: 'primary', label: 'More', link: 'https://gooble.com' },
          { type: 'secondary', label: 'About', link: 'https://gooble.com' },
        ]"
      />
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
