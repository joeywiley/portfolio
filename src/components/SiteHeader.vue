<script lang="ts" setup></script>

<template>
  <nav>
    <span class="nav-name">Joey Wiley</span>
    <ul class="nav-links">
      <li><a href="#work">Work</a></li>
      <li><a href="#about">About</a></li>
      <li><a href="#contact">Contact</a></li>
    </ul>
  </nav>
</template>

<style lang="scss" scoped>
nav {
  display: flex;
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  justify-content: space-between;
  align-items: center;
  z-index: 20;
  background: linear-gradient(to bottom, color.adjust($bg, $alpha: -0.5) 0%, transparent 100%);
  padding: 24px 48px; // 24 or 28?
}

.nav-name {
  color: $text-primary;
  font-size: 1.3rem;
  font-family: 'JetBrains Mono', monospace;
  letter-spacing: 0.08em;
}

.nav-links {
  display: flex;
  gap: 36px;

  a {
    position: relative;
    transition: color 0.2s ease;
    color: $text-secondary;
    font-weight: 500;
    font-size: 1.4rem;
    letter-spacing: 0.01em;

    // Extend clickable boundary
    &::after {
      position: absolute;
      top: -20px;
      right: -20px;
      bottom: -20px;
      left: -20px;
      content: '';
    }

    &:hover,
    &:focus-visible {
      color: $text-primary;
    }
  }
}
</style>
