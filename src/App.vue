<script setup>
import ReadFile from './components/ReadFile.vue';
import EmployeeHierarchy from './components/EmployeeHierarchy.vue';
import {ref, computed } from 'vue';
var file_text = defineModel('text');

// const msg = "Upload File";
const home_page = ref(true);

const togglePage = () => {
  home_page.value = !home_page.value;
}
const currPage = computed(() => {
  return home_page.value ? ReadFile : EmployeeHierarchy;
});

const msg = computed(() => {
  return home_page.value ? "Upload File" : file_text;
})

function generate_text(data) {
  file_text = data;
  togglePage();
}

</script>

<template>
  <main class="w-full">
    <div id="empdata">
      <component :is="currPage" @loadText="generate_text($event);" :msg="msg"></component>
    </div>
  </main>
</template>

<style scoped>
</style>
