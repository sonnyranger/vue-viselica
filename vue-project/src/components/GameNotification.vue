<script setup lang="ts">
import { ref } from 'vue'

const isVisible = ref(false)
const showNotification = () => {
  isVisible.value = true
}
const hideNotification = () => {
  isVisible.value = false
}

defineExpose({ showNotification, hideNotification })
</script>

<template>
  <div class="notification-container" :class="{ show: isVisible }">
    <p>Вы уже вводили этот символ</p>
  </div>
</template>
