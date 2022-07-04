<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import FileReader from './components/FileReader.vue'
import MainLanding from './views/MainLanding.vue'
import './index.css'
</script>

<template>
  <MainLanding>
  </MainLanding>
</template>

<style>
body {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  margin-top: 60px;
  background-color: black;

}

input {
  background-color: black;
}

.div-center {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
