<template>
  <div>
    <h1>Arrest Information</h1>
    <div v-if="arrestData.length">
      <div v-for="(arrest, index) in arrestData" :key="arrest.arrest_key">
        <p>Arrest Key: {{ arrest.arrest_key }}</p>
        <p>Arrest Date: {{ arrest.arrest_date }}</p>
        <p>PD Description: {{ arrest.pd_desc }}</p>
        <p>OFNS Description: {{ arrest.ofns_desc }}</p>
        <p>Location (Latitude, Longitude): {{ arrest.latitude }}, {{ arrest.longitude }}</p>
        <hr />
      </div>
    </div>
    <div v-else>
      <p>Loading arrest data...</p>
    </div>
    <div>
      <MyChart :chartData="chartData" :chartOptions="chartOptions" />
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import MyChart from './MyChart.vue'

export default {
  components: { MyChart },
  setup() {
    const arrestData = ref([])
    const chartData = ref(null)
    const chartOptions = ref({
      responsive: true,
      maintainAspectRatio: false,
    })

    const getArrestData = async () => {
      try {
        const res = await fetch('https://data.cityofnewyork.us/resource/8h9b-rp9u.json')
        const data = await res.json()
        arrestData.value = data

        // Prepare chart data
        const offenseCounts = data.reduce((acc, arrest) => {
          const offense = arrest.ofns_desc || 'Unknown'
          acc[offense] = (acc[offense] || 0) + 1
          return acc
        }, {})

        chartData.value = {
          labels: Object.keys(offenseCounts),
          datasets: [
            {
              label: 'Number of Arrests',
              data: Object.values(offenseCounts),
              backgroundColor: 'rgba(75, 192, 192, 0.2)',
              borderColor: 'rgba(75, 192, 192, 1)',
              borderWidth: 1,
            },
          ],
        }
      } catch (error) {
        console.error('Error fetching arrest data:', error)
      }
    }

    onMounted(() => {
      getArrestData()
    })

    return {
      arrestData,
      chartData,
      chartOptions,
    }
  },
}
</script>
