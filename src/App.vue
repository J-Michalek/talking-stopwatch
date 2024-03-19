<script setup lang="ts">
import { useInterval } from '@vueuse/core';





const {
  counter,
  pause,
  reset,
  resume,
} = useInterval(1000, {controls: true, immediate: false, callback(count) {
    playAudio(`src/assets/sounds/en_num_${count < 10 ? `0${count}` : count}.mp3`)
},})

let audio: HTMLAudioElement | null = null
function playAudio(filePath: string): void {
if (audio) {
  audio.pause()
  audio.remove()
}
   audio = new Audio(filePath)
   
  audio.addEventListener("ended", (): void => {
    audio?.remove()
  })
  audio.play()

}

</script>

<template>
 <div class="flex justify-center flex-col">

<h1 class="text-xl">{{ counter }}</h1>
<div class="flex gap-3">
  <button @click="resume">Start</button>
  <button @click="pause">Stop</button>
  <button @click="reset">Restart</button>
  
</div>
 </div>
</template>

<style scoped>


.flex {
  display: flex;
}

.justify-center {
  justify-content: center;
}

.flex-col {
  flex-direction: column;
}

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
