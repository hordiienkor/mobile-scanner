<script setup lang="ts">
import { ref } from 'vue';
import BarcodeScanner from './components/BarcodeScanner.vue'

  const showErrorMsg = ref<boolean>(false)
  const scannedCode = ref('')

  const startScane = ref(false)

  const toggleScane = () => {
    startScane.value = ! startScane.value
    if (startScane.value) {
      showErrorMsg.value = false
      scannedCode.value = ''
    }
  }

  const handleScanned = (code?: string) => {
    startScane.value = false
    if (!code || !/^\d+$/.test(code)) {
      showErrorMsg.value = true
      return
    }
    scannedCode.value = code
  }
</script>

<template>
  <div class="container">
    <BarcodeScanner
      v-if="startScane"
      @scanned="handleScanned"
    />
    <button class="barcode-btn" @click="toggleScane">
      {{ startScane ? 'Відмінити сканування' : 'Сканувати штрихкод' }}
    </button>
    <div>{{ `Відскановано код: ${scannedCode}` }}</div>
    <div v-if="showErrorMsg" style="color: red;">Не вдалось зчитати код. Спробуйте ще раз</div>
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

.barcode-btn {
  display: flex;
  flex-wrap: wrap;
  font-size: 20px;
  font-weight: 500;
  padding: 20px;
  justify-content: center;
  align-items: center;
}
</style>
