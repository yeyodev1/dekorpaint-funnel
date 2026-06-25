<script setup lang="ts">
import dekorpaintLogo from '@/assets/logos/image.png'
</script>

<template>
  <header class="booked-header">
    <img :src="dekorpaintLogo" alt="DekorPaint" class="booked-header__logo-img" />
  </header>
</template>

<style lang="scss" scoped>
@use '@/styles/colorVariables.module.scss' as colors;

.booked-header {
  width: 100%;
  padding: 1.25rem 1rem;
  display: flex;
  justify-content: center;
  background: colors.$OS-NAVY;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  position: sticky;
  top: 0;
  z-index: 100;

  &__logo-img {
    height: 36px;
    width: auto;
    display: block;
  }
}
</style>
