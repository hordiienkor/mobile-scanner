<script setup lang="ts">
import { BrowserMultiFormatReader, IScannerControls } from '@zxing/browser';
import { onMounted, onBeforeUnmount, ref } from 'vue'


const emit = defineEmits<{
    (e: 'scanned', value: string): void
}>()

const videoRef = ref<HTMLVideoElement | null>(null)
let codeReader: BrowserMultiFormatReader | null = null
const controlsRef = ref<IScannerControls | null>(null)

onMounted(async () => {
    codeReader = new BrowserMultiFormatReader()

    await navigator.mediaDevices.getUserMedia({ video: true });

    const devices = await BrowserMultiFormatReader.listVideoInputDevices()
    
    const selectedDeviceId = devices?.[0]?.deviceId
    console.log('deviceId', selectedDeviceId);

    if (selectedDeviceId && videoRef.value) {
        controlsRef.value = await codeReader.decodeFromVideoDevice(
        selectedDeviceId,
        videoRef.value,
        (result, error, controls) => {
            if (result) {
            emit('scanned', result.getText())
            controls.stop()    // останавливаем сразу после успешного сканирования
            }
        })
    }
})

onBeforeUnmount(() => {
    controlsRef.value?.stop()
})
</script>

<template>
    <video ref="videoRef" autoplay muted playsinline style="width: 100%; max-height: 300px" />
</template>
