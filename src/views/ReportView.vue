<template>
  <nav class="header">
    <div class="wrapper">
      <a href="/">
        <img src="../assets/images/logo.png" alt="Rep Analysis Logo" class="logo">
      </a>

      <div class="right-header">
        <div class="right-header-text">
          <small>Analisando</small>
          <small>Em tempo real</small>
        </div>
        <div class="company-logo">
          <div class="pulsing-circle"></div>
          <img src="https://logotyp.us/files/dell.svg" alt="Logo da Empresa" class="company-logo-img">
        </div>
      </div>
    </div>
  </nav>
  <div class="content">
    <div class="wrapper">
      <div class="realtime-charts">
        <apexchart width="450" type="pie" :options="optionsDonut" :series="seriesDonut"></apexchart>

        <apexchart width="600" type="line" :options="optionsLine1" :series="seriesLine1"></apexchart>
      </div>

      <div class="details">
        <div class="tweets">
          <h1 style="text-align: center; margin-top: 3rem;">
            <vue-feather type="trending-up" class="trend-icon"></vue-feather>
            Tweets
          </h1>
          
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

          <div class="card">
            <div class="badge">
              4
            </div>
            <p>Lorem ipsum, <mark>dolor</mark> sit amet consectetur adipisicing elit. <mark>Eligendi</mark> ratione error ea. Dolore necessitatibus quis labore rerum ex commodi, <mark>sunt doloribus ea</mark>, atque, aliquid fuga sit animi. Ex, recusandae animi.</p>

            <div class="score">
              83% negativo
            </div>
          </div>

          <div class="card">
            <div class="badge">
              5
            </div>
            <p>Lorem ipsum, <mark>dolor</mark> sit amet consectetur adipisicing elit. <mark>Eligendi</mark> ratione error ea. Dolore necessitatibus quis labore rerum ex commodi, <mark>sunt doloribus ea</mark>, atque, aliquid fuga sit animi. Ex, recusandae animi.</p>

            <div class="score">
              83% negativo
            </div>
          </div>

        </div>

        <div class="words-charts">
          <h1 style="text-align: center; margin-top: 3rem;">
            <vue-feather type="thumbs-up" class="trend-icon"></vue-feather>
            Top Palavras Positivas
          </h1>
          <apexchart width="600" type="bar" :options="optionsBar1" :series="seriesBar1"></apexchart>

          <h1 style="text-align: center; margin-top: 3rem;">
            <vue-feather type="thumbs-down" class="trend-icon"></vue-feather>
            Top Palavras Negativas
          </h1>
          <apexchart width="600" type="bar" :options="optionsBar1" :series="seriesBar1"></apexchart>
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
import VueFeather from 'vue-feather';
import VueApexCharts from "vue3-apexcharts";
import "@/assets/styles/report.css"

export default {
  name: 'ReportView',
  components: {
    apexchart: VueApexCharts,
    VueFeather
  },
  data: function() {
    return {
      optionsLine1: {
        markers: {
            size: 5,
        },
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
        yaxis: {
          title: {
            text: "Quantidade de Tweets",
            style: {
              fontSize: '14px',
            }
          }
        },
        xaxis: {
          title: {
            text: "Tempo",
            style: {
              fontSize: '14px',
            }
          }
        },
      },

      seriesLine1: [
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
      ],
      
      optionsBar1: {
        chart: {
          fontFamily: 'Comfortaa, cursive',
          id: 'realtime-chart',
          toolbar: {
            show: false,
          },
        },
        colors: ['#ae13a6'],
        grid: {
          show: false
        },
        xaxis: {
          title: {
            text: "SHAP",
            style: {
              fontSize: '14px',
            }
          },
          categories: ['Quebrou', 'Amo', 'Apaixonada', 'Perfeito', 'Delícia'],
        },
        plotOptions: {
          bar: {
            borderRadius: 20,
            horizontal: true,
          }
        },
      },

      seriesBar1: [
        {
          data: [30, 40, 45, 50, 49]
        },
      ],

      optionsDonut: {
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
        labels: ['Positivo', 'Neutro', 'Negativo'],
        legend: {
          show: true,
        }
      },

      seriesDonut: [44, 55, 15],
    }
  }
}
</script>