<script setup lang="ts">
import { ref } from 'vue'
import MdEditor from './components/MdEditor.vue'
import NavBar from "@/components/NavBar.vue";

</script>

<template>
  <NavBar/>
  <main class="main">
    <MdEditor></MdEditor>
  </main>
</template>

<style scoped>
.main{
  /* height: calc(100vh - 100px) ; */
  height: 100%;
  /* padding-top: 50px; */
  box-sizing: border-box;
}

</style>
