<script setup lang="ts">
import { ref } from 'vue';
import QRScanner from './components/QRScanner.vue'
import QRGenerator from './components/QRGenerator.vue'
import PWABadge from './components/PWABadge.vue'

const activeTab = ref<'scan' | 'generate'>('scan');
</script>

<template>
  <div class="app-header">
    <h1 class="glow-text">Ultimate QR</h1>
    <div class="tabs">
      <button :class="{ active: activeTab === 'scan' }" @click="activeTab = 'scan'" class="tab-btn">Scan</button>
      <button :class="{ active: activeTab === 'generate' }" @click="activeTab = 'generate'" class="tab-btn">Generate</button>
    </div>
  </div>

  <main class="main-content">
    <transition name="fade" mode="out-in">
      <div :key="activeTab" class="tab-content">
        <QRScanner v-if="activeTab === 'scan'" />
        <QRGenerator v-if="activeTab === 'generate'" />
      </div>
    </transition>
  </main>

  <PWABadge />
</template>

<style scoped>
.app-header {
  margin-bottom: 2rem;
}

.glow-text {
  font-weight: 800;
  background: linear-gradient(135deg, #646cff, #a855f7);
  background-clip: text;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  margin-bottom: 1.5rem;
}

.tabs {
  display: inline-flex;
  background: rgba(0, 0, 0, 0.2);
  padding: 0.5rem;
  border-radius: 12px;
  gap: 0.5rem;
}

.tab-btn {
  background: transparent;
  color: rgba(255, 255, 255, 0.7);
  border: none;
  border-radius: 8px;
  padding: 0.75rem 2rem;
  font-weight: 600;
  transition: all 0.3s ease;
}

.tab-btn:hover {
  color: white;
  background: rgba(255, 255, 255, 0.05);
}

.tab-btn.active {
  background: linear-gradient(135deg, #646cff, #a855f7);
  color: white;
  box-shadow: 0 4px 12px rgba(100, 108, 255, 0.3);
}

.main-content {
  min-height: 450px;
  display: flex;
  justify-content: center;
}

.tab-content {
  width: 100%;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}
</style>
