<template>
  <div class="chart-container">
    <LineChartGenerator
      :chart-options="chartOptions"
      :chart-data="chartData"
      :chart-id="chartId"
      :dataset-id-key="datasetIdKey"
      :plugins="plugins"
      :css-classes="cssClasses"
      :styles="styles"
      :width="width"
      :height="height"
    />
    <button @click="clearTable">Limpiar datos</button>
  </div>
  </template>
  
  <script>
  import { Line as LineChartGenerator } from 'vue-chartjs/legacy'
  
  import {
    Chart as ChartJS,
    Title,
    Tooltip,
    Legend,
    LineElement,
    LinearScale,
    CategoryScale,
    PointElement
  } from 'chart.js'
  
  ChartJS.register(
    Title,
    Tooltip,
    Legend,
    LineElement,
    LinearScale,
    CategoryScale,
    PointElement
  )
  
  export default {
    name: 'LineChart',
    components: {
      LineChartGenerator
    },
    props: {
      tableData: [Array, Object],
      chartTitle: String,
      chartId: {
        type: String,
        default: 'line-chart'
      },
      datasetIdKey: {
        type: String,
        default: 'label'
      },
      width: {
        type: Number,
        default: 400
      },
      height: {
        type: Number,
        default: 400
      },
      cssClasses: {
        default: '',
        type: String
      },
      styles: {
        type: Object,
        default: () => {}
      },
      plugins: {
        type: Array,
        default: () => []
      }
    },
    data() {
      return {
        chartData: {
          labels: ['0.0','0.1','0.2','0.3','0.4','0.5','0.6','0.7','0.8','0.9','1.0'],
          datasets: [
            {
              label: 'Demo 01',
              backgroundColor: '#f87979',
              data: [0.1, 0.2, 0.3, 0.4, 0.5, 0.5, 0.6]
            }
          ]
        },
        chartOptions: {
          responsive: true,
          maintainAspectRatio: true,
          aspectRatio: 1|1
        }
      }
    },
    methods: {
      clearTable() {
        this.chartData.datasets = [];
        this.$emit('chartCleared');
      }
    },
    watch: {
      tableData(newVal) {
        if(newVal.length > 0) {
          let labels = [];
          let data = [];
      
          for(let i in newVal) {
            labels.push(newVal[i].x);
            data.push(newVal[i].y);
          }
      
          this.chartData.labels = labels;
          this.chartData.datasets.push({
            label: this.$props.chartTitle,
            backgroundColor: '#f87979',
            data
          });
        }
      }
    },
    mounted() {
      this.chartData.datasets = [];
    }
  }
</script>

<style>
.chart-container {
  padding: 0.75em; 
}
.chart-container button {
  margin-top: 1em;
}
</style>
  