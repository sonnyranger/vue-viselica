<script setup lang="ts">
import type { GameStatus } from '../types/GameStatus'
import { ref } from 'vue'

interface Props {
  word: string
}

defineProps<Props>()

const gameStatus = ref<GameStatus | null>(null)

const isVisible = ref(false)
const showNotificationPop = (status: GameStatus) => {
  gameStatus.value = status
  isVisible.value = true
}
const hideNotificationPop = (status: GameStatus) => {
  isVisible.value = false
}

defineExpose({ showNotificationPop, hideNotificationPop })

const emit = defineEmits<{ (e: 'restart'): void }>()
</script>

<template>
  <div v-show="isVisible">
    <div class="popup">
      <h2 v-if="gameStatus === 'win'">Поздравляю, вы победили! 😃</h2>
      <template v-else>
        <h2>Вы проиграли 😕</h2>

        <h3>Имя: {{ word }}</h3>
      </template>
      <button @click="emit('restart')">Сыграть еще раз</button>
    </div>
  </div>
</template>
йц
