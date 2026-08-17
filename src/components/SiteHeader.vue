<template>
  <nav>
    <span class="nav-name">Joey Wiley</span>
    <ul class="nav-links">
      <li><a href="#work" class="btn-tertiary">Work</a></li>
      <li><a href="#about" class="btn-tertiary">About</a></li>
      <li><a href="#contact" class="btn-tertiary">Contact</a></li>
    </ul>
  </nav>
</template>

<script lang="ts" setup></script>

<style lang="scss" scoped>
nav {
  display: flex;
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  justify-content: space-between;
  align-items: center;
  gap: $header-gap;
  z-index: 20;
  background: linear-gradient(to bottom, color.adjust($bg, $alpha: -0.5) 0%, transparent 100%);
  padding: $gutters-v $gutters-h;
}

.nav-name {
  color: $text-primary;
  font-size: 1.3rem;
  font-family: 'JetBrains Mono', monospace;
  letter-spacing: 0.08em;
}

.nav-links {
  display: flex;
  gap: $header-gap;
}
</style>
