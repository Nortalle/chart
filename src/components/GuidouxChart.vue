<template>
<div class="filled">
   <canvas id="canvas"></canvas>
   <button
   class="topRight"
   v-if="zoomed"
    @click="resetZoom()">
    Reset Zoom
    </button>
</div>
</template>

<script>
import Chart from 'chart.js';
import 'chartjs-plugin-zoom';
import 'chartjs-plugin-datalabels';

export default {
  props: ['coordinates'],
  components: {
  },
  mounted() {
    this.ctx = document.getElementById('canvas').getContext('2d');
    this.createChart();
  },
  methods: {
    createChart() {
      const datasets = [];

      for (let index = 0; index < this.coordinates.length; index += 1) {
        const color = this.getColor(index);
        const dataset = {
          label: this.coordinates[index].name,
          backgroundColor: color,
          borderColor: color,
          data: this.coordinates[index].points.sort((a, b) => ((a.x > b.x) ? 1 : -1)),
          fill: false,
          showLine: true,
        };

        datasets.push(dataset);
      }
      const config = {
        type: 'scatter',
        data: {
          datasets,
        },
        options: {
          responsive: true,
          animation: {
            duration: 0,
          },
          responsiveAnimationDuration: 0,
          elements: {
            point: {
              radius: 0,
            },
          },
          title: {
            display: true,
            text: 'Chart.js Line Chart',
          },
          scales: {
            xAxes: [{
              display: true,
              ticks: {
                maxTicksLimit: 10,
              },
              scaleLabel: {
                display: true,
                labelString: 'psi[°]',
              },
            }],
            yAxes: [{
              display: true,
              scaleLabel: {
                display: true,
                labelString: 'V[kN]',
              },
            }],
          },
          hover: {
            mode: 'index',
            intersect: false,
            animationDuration: 0,
          },
          plugins: {
            datalabels: {
              textAlign: 'center',
              clip: true,
              borderWidth: 1,
              font: {
                weight: 'bold',
              },

              align(context) {
                const { datasetIndex } = context;
                return 45 + 90 * datasetIndex;
              },
              backgroundColor(context) {
                return context.active ? context.dataset.backgroundColor : null;
              },
              borderColor(context) {
                return context.active ? context.dataset.backgroundColor : null;
              },
              borderRadius(context) {
                return context.active ? 0 : 0;
              },
              color(context) {
                return context.active ? 'white' : null;
              },
              formatter: (value, context) => (context.active
                ? `x:${value.x.toFixed(5)}\ny:${value.y.toFixed(1)}`
                : ''),
            },
            zoom: {
              zoom: {
                enabled: true,
                drag: true,
                mode: 'xy',
                speed: 0,
                onZoom: () => { this.zoomed = true; },
              },
            },
          },
        },
      };
      this.chart = new Chart(this.ctx, config);
    },
    resetZoom() {
      this.zoomed = false;
      this.chart.resetZoom();
    },
    getColor(index) {
      const colors = Object.keys(this.chartColors);
      return colors[index % colors.length];
    },
  },
  data() {
    return {
      ctx: null,
      chart: null,
      chartColors: {
        blue: 'rgb(54, 162, 235)',
        red: 'rgb(255, 99, 132)',
        green: 'rgb(75, 192, 192)',
        orange: 'rgb(255, 159, 64)',
        yellow: 'rgb(255, 205, 86)',
        purple: 'rgb(153, 102, 255)',
        grey: 'rgb(231,233,237)',
      },
      zoomed: false,
    };
  },
};
</script>

<style>
  .filled {
    overflow:hidden;
  }
  .topRight {position: absolute; top:10%; right:2.5%;
  }
</style>
