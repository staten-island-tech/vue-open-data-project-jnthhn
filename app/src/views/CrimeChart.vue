<template>
  <div>
    <h2>Bad Guy Data</h2>
    <div v-if="loading">Loading...</div>
    <div v-else-if="error">{{ error }}</div>
    <div v-else>
      <div class="card-container">
        <div v-for="(crime, index) in crimeList" :key="crime.rpt_id" class="crime-card">
          <p>Report ID: {{ crime.arrest_key || 'Unknown' }}</p>
          <p>Offense: {{ crime.ofns_desc || 'Unknown' }}</p>
          <p>Perpetrator Race: {{ crime.perp_race || 'Unknown' }}</p>
          <p>Borough: {{ crime.arrest_boro || 'Unknown' }}</p>
        </div>
      </div>

      <h3>Crime Types</h3>
      <BarChart :chart-data="crimeTypeData" />
      <h3>Race Distribution</h3>
      <PieChart :chart-data="perpRaceData" />
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import BarChart from '../components/BarChart.vue'
import PieChart from '../components/PieChart.vue'

export default {
  components: { BarChart, PieChart },
  setup() {
    const crimeList = ref([])
    const crimeTypeData = ref(null)
    const perpRaceData = ref(null)
    const loading = ref(true)
    const error = ref(null)

    async function fetchCrimeData() {
      const url = 'https://data.cityofnewyork.us/resource/8h9b-rp9u.json'
      try {
        const response = await fetch(url)
        const data = await response.json()

        const randomizedData = data.sort(() => Math.random() - 0.5)
        crimeList.value = randomizedData.slice(0, 24)

        const crimeCounts = data.reduce((acc, crime) => {
          if (crime.ofns_desc) {
            acc[crime.ofns_desc] = (acc[crime.ofns_desc] || 0) + 1
          }
          return acc
        }, {})

        const raceCounts = data.reduce((acc, crime) => {
          if (crime.perp_race) {
            acc[crime.perp_race] = (acc[crime.perp_race] || 0) + 1
          }
          return acc
        }, {})

        crimeTypeData.value = {
          labels: Object.keys(crimeCounts),
          datasets: [
            {
              label: 'Number of Crimes',
              backgroundColor: '#42A5F5',
              data: Object.values(crimeCounts),
            },
          ],
        }

        perpRaceData.value = {
          labels: Object.keys(raceCounts),
          datasets: [
            {
              label: 'Perpetrator Race',
              backgroundColor: ['#FF6384', '#36A2EB', '#FFCE56', '#4CAF50', '#9966FF'],
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

    return { crimeList, crimeTypeData, perpRaceData, loading, error }
  },
}
</script>

<style scoped>
.card-container {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
  justify-content: center;
}

.crime-card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  padding: 16px;
  width: 250px;
  text-align: center;
}

h3 {
  margin-top: 24px;
}
</style>
