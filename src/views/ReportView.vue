<template>
  <nav class="header">
    <div class="wrapper">
      <a href="/">
        <img src="../assets/images/logo.png" alt="Rep Analysis Logo" class="logo">
      </a>
    </div>
  </nav>
  <div class="content">
    <div class="wrapper">
      <apexchart width="500" type="line" :options="options" :series="series"></apexchart>

      <h1 style="text-align: center; margin-top: 3rem;">Tweets</h1>

      <div class="tweets">
        <div class="card">
          <div class="badge">
            1
          </div>
          <p>Lorem ipsum, <mark>dolor</mark> sit amet consectetur adipisicing elit. <mark>Eligendi</mark> ratione error ea. Dolore necessitatibus quis labore rerum ex commodi, <mark>sunt doloribus ea</mark>, atque, aliquid fuga sit animi. Ex, recusandae animi.</p>

          <div class="score">
            94% negativo
          </div>
        </div>

        <div class="card">
          <div class="badge">
            2
          </div>
          <p>Lorem ipsum, <mark>dolor</mark> sit amet consectetur adipisicing elit. <mark>Eligendi</mark> ratione error ea. Dolore necessitatibus quis labore rerum ex commodi, <mark>sunt doloribus ea</mark>, atque, aliquid fuga sit animi. Ex, recusandae animi.</p>

          <div class="score">
            94% negativo
          </div>
        </div>

        <div class="card">
          <div class="badge">
            3
          </div>
          <p>Lorem ipsum, <mark>dolor</mark> sit amet consectetur adipisicing elit. <mark>Eligendi</mark> ratione error ea. Dolore necessitatibus quis labore rerum ex commodi, <mark>sunt doloribus ea</mark>, atque, aliquid fuga sit animi. Ex, recusandae animi.</p>

          <div class="score">
            83% negativo
          </div>
        </div>
      </div>
      
    </div>
  </div>
  <footer class="footer">
    <div class="wrapper">
      <p>&copy; 2023 Rep Analysis</p>
      <p> &lt; Pantanal.dev &gt; </p>
    </div>
  </footer>
</template>

<script>
import "@/assets/styles/report.css"
import VueApexCharts from "vue3-apexcharts";

export default {
  name: 'ReportView',
  components: {
    apexchart: VueApexCharts,
  },
  data: function() {
    return {
      options: {
        stroke: {
          curve: 'smooth',
        },
        chart: {
          fontFamily: 'Comfortaa, cursive',
          id: 'realtime-chart',
          toolbar: {
            show: false,
          },
        },
        colors: ['#08f26e', '#cccccc', '#f7345e'],
        grid: {
          show: false
        },
      },
      series: [
        {
          name: 'Positivo',
          data: [30, 40, 45, 50, 49, 60, 70, 91]
        },
        {
          name: 'Neutro',
          data: [40, 50, 55, 60, 59, 70, 90, 100]
        },
        {
          name: 'Negativo',
          data: [10, 80, 50, 65, 14, 23, 71, 80]
        }
      ]
    }
  }
}
</script>