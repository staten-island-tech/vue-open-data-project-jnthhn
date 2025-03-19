<template>
  <div>
    <h1>Arrest Information</h1>
    <div v-if="arrestData.length">
      <div v-for="(arrest, index) in arrestData" :key="arrest.arrest_key">
        <p>Arrest Key: {{ arrest.arrest_key }}</p>
        <p>Arrest Date: {{ arrest.arrest_date }}</p>
        <p>PD Description: {{ arrest.pd_desc }}</p>
        <p>OFNS Description: {{ arrest.ofns_desc }}</p>
        <p>
          Location (Latitude, Longitude): {{ arrest.latitude }},
          {{ arrest.longitude }}
        </p>
        <hr />
      </div>
    </div>
    <div v-else>
      <p>Loading arrest data...</p>
    </div>
    <div>
      <!-- Assign a ref to the canvas element -->
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import { Chart, registerables } from 'chart.js'

Chart.register(...registerables)

const arrestData = ref([])
const chartCanvas = ref(null)
let chartInstance = null

const getArrest = async () => {
  try {
    const res = await fetch('https://data.cityofnewyork.us/resource/8h9b-rp9u.json')
    const data = await res.json()
    arrestData.value = data
  } catch (error) {
    console.error('Error fetching arrest data:', error)
  }
}

const createChart = () => {
  if (!chartCanvas.value || !arrestData.value.length) return

  const offenseCounts = arrestData.value.reduce((acc, arrest) => {
    const offense = arrest.ofns_desc || 'Unknown'
    acc[offense] = (acc[offense] || 0) + 1
    return acc
  }, {})

  const labels = Object.keys(offenseCounts)
  const data = Object.values(offenseCounts)

  if (chartInstance) {
    chartInstance.destroy()
  }

  chartInstance = new Chart(chartCanvas.value.getContext('2d'), {
    type: 'bar',
    data: {
      labels,
      datasets: [
        {
          label: 'Number of Arrests',
          data,
          backgroundColor: 'rgba(75, 192, 192, 0.2)',
          borderColor: 'rgba(75, 192, 192, 1)',
          borderWidth: 1,
        },
      ],
    },
    options: {
      responsive: true,
      maintainAspectRatio: false,
    },
  })
}

onMounted(() => {
  getArrest()
})

// Watch for changes in arrestData and create the chart when data is loaded
watch(arrestData, () => {
  createChart()
})
</script>
