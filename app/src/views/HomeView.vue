<template>
  <div></div>
</template>
<script setup>
import { ref, onMounted } from 'vue'
const arrest_key = ref('')
async function getArrest() {
  let res = await fetch('https://data.cityofnewyork.us/resource/8h9b-rp9u.json')
  let data = await res.json()
  arrest_key.value = data.results
}
onMounted(() => {
  getArrest()
})


<style scoped></style>
