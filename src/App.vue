<script setup lang="ts">
import QRLogic from './components/QRLogic.vue'
import PWABadge from './components/PWABadge.vue'
</script>

<template>
  <QRLogic></QRLogic>
  <PWABadge />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
