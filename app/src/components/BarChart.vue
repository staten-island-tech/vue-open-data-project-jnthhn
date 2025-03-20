<template>
  <div class="chart-wrapper">
    <canvas ref="chartCanvas"></canvas>
  </div>
</template>

<script>
import { ref, onMounted, watch } from 'vue'
import { Chart, registerables } from 'chart.js'

Chart.register(...registerables)

export default {
  props: {
    chartData: Object,
  },
  setup(props) {
    const chartCanvas = ref(null)
    let chartInstance = null

    onMounted(() => {
      if (props.chartData) {
        createChart()
      }
    })

    watch(props, () => {
      if (chartInstance) chartInstance.destroy()
      createChart()
    })

    function createChart() {
      if (!props.chartData) return
      chartInstance = new Chart(chartCanvas.value, {
        type: 'bar',
        data: props.chartData,
        options: {
          scales: {
            y: {
              beginAtZero: true, // Start from 0
              ticks: {
                stepSize: 2, // Adjust step size
              },
            },
            x: {
              ticks: {
                autoSkip: false, // Prevent label skipping
              },
            },
          },
        },
      })
    }

    return { chartCanvas }
  },
}
</script>

<style scope>
.chart-wrapper {
  width: 90vw; /* Take 90% of the viewport width */
  height: 80vh; /* Take 80% of the viewport height */
  max-width: 1200px; /* Optional: Set max width */
  margin: auto; /* Center the chart */
  display: flex;
  align-items: center;
  justify-content: center;
}
canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>
