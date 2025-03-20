<template>
  <div class="card">
    <h2>Crime Data</h2>
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <ul>
        <li v-for="(count, arrest_key) in crimeData" :key="arrest_key">{{ arrest_key }}: {{ count }} crimes</li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const crimeData = ref({})
    const loading = ref(true)
    const error = ref(null)

    async function fetchCrimeData() {
      const url = 'https://data.cityofnewyork.us/resource/8h9b-rp9u.json'
      try {
        const response = await fetch(url)
        const data = await response.json()

  


    onMounted(fetchCrimeData)

    return { crimeData, loading, error }
  },
}
</script>

<style scoped>
.card {
  width: 200px;
  padding: 15px;
  margin: 10px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  text-align: center;
  background-color: #fff;
  cursor: pointer;
  transition:
    transform 250ms,
    filter 250ms;
}
</style>
