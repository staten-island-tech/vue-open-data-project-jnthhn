<template>
  <div>
    <h1>Arrest Information</h1>
    <div v-if="arrest_key.length">
      <div v-for="(arrest, index) in arrest_key" :key="arrest.arrest_key">
        <p><strong>Arrest Key:</strong> {{ arrest.arrest_key }}</p>
        <p><strong>Arrest Date:</strong> {{ arrest.arrest_date }}</p>
        <p><strong>PD Description:</strong> {{ arrest.pd_desc }}</p>
        <p><strong>OFNS Description:</strong> {{ arrest.ofns_desc }}</p>
        <p>
          <strong>Location (Latitude, Longitude):</strong> {{ arrest.latitude }},
          {{ arrest.longitude }}
        </p>
        <hr />
      </div>
    </div>
    <div v-else>
      <p>Loading arrest data...</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { Bar } from 'vue-chartjs'
const arrest_key = ref([])

async function getArrest() {
  let res = await fetch('https://data.cityofnewyork.us/resource/8h9b-rp9u.json')
  let data = await res.json()
  arrest_key.value = data
}

onMounted(() => {
  getArrest()
})
</script>

<style scoped></style>
