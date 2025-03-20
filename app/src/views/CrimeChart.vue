<template>
  <div>
    <h2>Crime Data by Perpetrator Race</h2>
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <BarChart v-else :chart-data="chartData" />
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import BarChart from '../components/BarChart.vue'

export default {
  components: { BarChart },
  setup() {
    const chartData = ref(null)
    const loading = ref(true)
    const error = ref(null)

    async function fetchCrimeData() {
      const url = 'https://data.cityofnewyork.us/resource/8h9b-rp9u.json'
      try {
        const response = await fetch(url)
        const data = await response.json()

        // Count occurrences of each race
        const raceCounts = data.reduce((acc, crime) => {
          if (crime.perp_race) {
            acc[crime.perp_race] = (acc[crime.perp_race] || 0) + 1
          }
          return acc
        }, {})

        chartData.value = {
          labels: Object.keys(raceCounts),
          datasets: [
            {
              label: 'Number of Crimes',
              backgroundColor: '#42A5F5',
              data: Object.values(raceCounts),
            },
          ],
        }
      } catch (err) {
        error.value = 'Error fetching data'
        console.error('Error fetching data:', err)
      } finally {
        loading.value = false
      }
    }

    onMounted(fetchCrimeData)

    return { chartData, loading, error }
  },
}
</script>
