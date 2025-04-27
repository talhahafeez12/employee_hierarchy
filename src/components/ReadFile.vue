<script setup>
  import { defineEmits } from 'vue';
  defineProps(['msg']);
  const emit = defineEmits(["loadText"]);
  function loadTextFromFile(ev) {
    const file = ev.target.files[0];
    const reader = new FileReader();
    reader.onload = e => emit("loadText", e.target.result);
    reader.readAsText(file);
  }
</script>

<template>
  <main class="w-full">
    <div class="greetings">
      <h1 class="green">{{ msg }}</h1>
      <h3>
        Please import the csv file containing data.
      </h3>
      <label class="text-reader">
        <input type="file" @change="loadTextFromFile" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full"/>
      </label>
    </div>
  </main>
  
</template>

<style scoped>
</style>
