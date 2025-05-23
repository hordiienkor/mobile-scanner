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
    
    const backCamera = devices.find(d =>
        /back|rear|environment/i.test(d.label)
    )
    const selectedDeviceId = (backCamera || devices[0])?.deviceId

    if (selectedDeviceId && videoRef.value) {
        controlsRef.value = await codeReader.decodeFromVideoDevice(
        selectedDeviceId,
        videoRef.value,
        (result, error, controls) => {
            if (result) {
            emit('scanned', result.getText())
            controls.stop()
            }
        })
    }
})

onBeforeUnmount(() => {
    controlsRef.value?.stop()
})
</script>

<template>
    <video ref="videoRef" autoplay muted playsinline style="width: 100vw; max-width: 100%;; max-height: 500px" />
</template>
