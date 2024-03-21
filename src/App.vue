<script setup lang="ts">
import { useInterval } from "@vueuse/core";
import { computed, ref } from "vue";

const { counter, pause, reset, resume } = useInterval(1000, {
  controls: true,
  immediate: false,
  callback(count) {
    speak(count);
  },
});

const voices = ref<SpeechSynthesisVoice[]>([]);
const selectedVoice = ref<SpeechSynthesisVoice | null>(null);

speechSynthesis.onvoiceschanged = () => {
  const v = speechSynthesis.getVoices();
  if (v.length > 0) {
    voices.value = speechSynthesis.getVoices();
    selectedVoice.value = voices.value[0];
  }
};

const modelVoice = computed({
  get() {
    return selectedVoice.value?.name ?? "";
  },
  set(name: string) {
    selectedVoice.value =
      voices.value.find((voice) => voice.name === name) ?? null;
  },
});

function speak(count: number): void {
  const utterance = new SpeechSynthesisUtterance(String(count));

  utterance.voice = selectedVoice.value;
  utterance.lang = selectedVoice.value!.lang;
  utterance.rate = 2;
  speechSynthesis.cancel();
  speechSynthesis.speak(utterance);
}
</script>

<template>
  <div class="flex justify-center flex-col">
    <select v-if="selectedVoice != null" v-model="modelVoice" name="voices">
      <option v-for="voice of voices" :key="voice.name" :value="voice.name">
        {{ voice.name }}
      </option>
    </select>
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
