<script setup lang="ts">
import { ref } from 'vue';
import BarcodeScanner from './components/BarcodeScanner.vue'

  const scannedCode = ref('')

  const startScane = ref(false)

  const toggleScane = () => {
    startScane.value = ! startScane.value
    if (startScane.value) {
      scannedCode.value = ''
    }
  }

  const handleScanned = (code?: string) => {
    scannedCode.value = code
    startScane.value = false
  }
</script>

<template>
  <div class="container">
    <BarcodeScanner
      v-if="startScane"
      @scanned="handleScanned"
    />
    <button class="barcode" @click="toggleScane">
      {{ startScane ? 'Відмінити сканування' : 'Сканувати штрихкод' }}
    </button>
    <div>{{ `Відскановано код: ${scannedCode}` }}</div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  width: min(100%, 100vw);
  max-width: 100vw;
  box-sizing: border-box;
  flex-direction: column;
}

.barcode {
  display: flex;
  flex-wrap: wrap;
  font-size: 20px;
  font-weight: 500;
  padding: 20px;
}
</style>
