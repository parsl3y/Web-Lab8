<template>
  <div class="chart-container">
    <canvas id="myChart"></canvas>
  </div>
  <router-link to="./student" class="text-blue-500 hover:underline">Перейти до продуктів.</router-link>
  <Title> Graphic </Title>
</template>

<script>
import Chart from 'chart.js/auto'; // оптимальна версія chart

export default {
  mounted() {
    this.renderChart();
  },
  methods: {
    renderChart() {
      const ctx = document.getElementById('myChart').getContext('2d');
      const myChart = new Chart(ctx, {
        type: 'line',
        data: {
          labels: ['2022 (Q3)', '2022 (Q4)', '2023 (Q1)', '2023 (Q2)', '2023 (Q3)', '2023 (Q4)', '2024 (Q1)', '2024 (Q2)'],
          datasets: [{
            label: 'Data',
            data: [30, 22.5, 40, 20, 25, 10, 15, 22.5],
            backgroundColor: 'white',
            borderColor: 'Green',
            borderWidth: 1,
            pointRadius: 0 // точки на верхівках 0
          }]
        },
        options: {
          scales: {
            x: {
              grid: {
                display: false //  вертикальні лінії 0
              }
            },
            y: {
              max: 50,
              min: 0,
              ticks: {
                stepSize: 10
              }
            }
          }
        }
      });
    }
  }
}
</script>

<style scoped>
.chart-container {
  background-color: white;
  width: 60%;

}
</style>
