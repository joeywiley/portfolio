<script lang="ts" setup>
import ScrollButton from '@/components/common/ScrollButton.vue'
</script>

<template>
  <section id="title" class="title-page">
    <div class="hero">
      <span class="degree">Electrical Engineering Major</span>
      <h1>Some blurb about me <em>right</em> here.</h1>
      <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc in diam fermentum felis finibus placerat quis id velit.</p>
      <div class="actions">
        <a href="#work" class="btn-primary">See my work</a>
        <a href="#contact" class="btn-secondary">Get in touch</a>
      </div>
      <div class="scroll-cue"></div>
    </div>

    <ScrollButton class="scroll-hint" icon="keyboard_arrow_down" label="SCROLL" , section="work" />
  </section>
</template>

<style lang="scss" scoped>
.title-page {
  scroll-snap-align: start;
}

.hero {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 0 48px;
  max-width: 900px;
  min-height: 100vh;

  .degree {
    margin-bottom: 24px;
    color: $text-tertiary;
    font-size: 1.3rem;
    font-family: $font-mono;
    letter-spacing: 0.06em;
    text-shadow: $shadow;

    .dot {
      color: $accent;
      user-select: none;
    }
  }

  h1 {
    max-width: 11ch;
    color: $text-primary;
    font-weight: 500;
    font-size: clamp(40px, 7vh, 84px);
    line-height: 1.05;
    font-family: $font-accent;
    letter-spacing: -0.01em;
    text-shadow: $shadow;

    em {
      color: $accent;
      font-style: italic;
    }
  }

  p {
    margin-top: 20px;
    max-width: 48ch;
    color: $text-secondary;
    font-size: 1.8rem;
    line-height: 1.6;
    text-shadow: $shadow;
  }
}

.actions {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-top: 40px;
}

.scroll-hint {
  position: absolute;
  bottom: 24px;
  left: 48px;
}
</style>
