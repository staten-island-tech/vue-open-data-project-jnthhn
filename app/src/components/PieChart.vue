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
        type: 'pie',
        data: props.chartData,
        options: {
          responsive: true,
          plugins: {
            legend: {
              display: true,
              position: 'top',
            },
          },
        },
      })
    }

    return { chartCanvas }
  },
}
</script>

<style scoped>
.chart-wrapper {
  width: 60vw;
  height: 60vh;
  max-width: 600px;
  margin: auto;
  display: flex;
  align-items: center;
  justify-content: center;
}
canvas {
  width: 100% !important;
  height: 100% !important;
}
</style>
