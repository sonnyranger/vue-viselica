<script setup lang="ts">
import GameErrors from './components/GameErrors.vue'
import GameFigure from './components/GameFigure.vue'
import GameHeader from './components/GameHeader.vue'
import GameNotification from './components/GameNotification.vue'
import GamePopup from './components/GamePopup.vue'
import GameWord from './components/GameWord.vue'
import axios from 'axios'

import { computed, ref, watch } from 'vue'

const word = ref('')

const getRandomWord = async () => {
  try {
    const { data } = await axios<{ FirstName: string }>(
      'https://api.randomdatatools.ru/?unescaped=false&params=FirstName'
    )

    word.value = data.FirstName.toLowerCase()
  } catch (error) {
    console.log(error)
    word.value = ''
  }
}

getRandomWord()

const letters = ref<string[]>([])

const correctLetters = computed(() => {
  return letters.value.filter((el) => word.value.includes(el))
})

const wrongLetters = computed(() => {
  return letters.value.filter((el) => !word.value.includes(el))
})

const isStatusLose = computed(() => {
  return wrongLetters.value.length >= 6
})

const isStatusWin = computed(() => {
  return [...word.value].every((el) => correctLetters.value.includes(el))
})

const notification = ref<InstanceType<typeof GameNotification> | null>(null)
const popup = ref<InstanceType<typeof GamePopup> | null>(null)

watch(wrongLetters, () => {
  if (isStatusLose.value) {
    popup.value?.showNotificationPop('lose')
  }
})

watch(correctLetters, () => {
  if (isStatusWin.value) {
    popup.value?.showNotificationPop('win')
  }
})

window.addEventListener('keydown', ({ key }) => {
  if (isStatusLose.value || isStatusWin.value) return
  if (letters.value.includes(key)) {
    notification.value?.showNotification()
    setTimeout(() => {
      notification.value?.hideNotification()
    }, 2000)
    return
  }
  if (/[а-яА-ЯёЁ]/.test(key)) {
    letters.value.push(key.toLowerCase())
  }
})

const restart = async () => {
  await getRandomWord()
  letters.value = []
  popup.value?.hideNotificationPop()
}
</script>

<template>
  <div id="app">
    <GameHeader />
    <div class="game-container">
      <GameFigure :wrong-letters-count="wrongLetters.length" />

      <GameErrors :wrong-letters="wrongLetters" />

      <GameWord :word="word" :correct-letters="correctLetters" />
    </div>

    <GamePopup ref="popup" :word="word" @restart="restart" />

    <GameNotification ref="notification" />
  </div>
</template>
