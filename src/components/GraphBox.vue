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
        colorPool:[
        "#1CD4D4", "#14245A", "#1DE5BC", "#176CA1", "#C7F9ED", "#1AC9E7", "#6EF0D2", "#18ABDD",
        "#EA548B", "#29066B", "#EA7369", "#7D3AC0", "#FBEAE7", "#DC4BB3", "#EFA68F", "#B04BCF",
        "#ED9B3A", "#830402", "#EABD3C", "#C02223", "#F7F4BE", "#EF7E32", "#E7E44F", "#DF542C"
        ],
        usedColors: [],
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
          aspectRatio: 1|1,
          plugins:{
            legend: {
              labels: {
                usePointStyle: true
              }
            }
          }
        }
      }
    },
    methods: {
      clearTable() {
        this.chartData.datasets = [];
        this.$emit('chartCleared');
      },
      chooseColor() {
        return this.colorPool[Math.round(Math.random()*this.colorPool.length-1)];
      }
    },
    watch: {
      tableData(newVal) {
        let dataTable;

        if(newVal.aprox && newVal.aprox.length > 0) {
          dataTable = newVal.aprox;
        } else if (newVal.exact && newVal.exact.length > 0) {
          dataTable = newVal.exact;
        } else {
          return;
        }

        let labels = [];
        let data = [];
    
        for(let i in dataTable) {
          labels.push(dataTable[i].x);
          data.push(dataTable[i].y);
        }
    
        this.chartData.labels = labels;
        this.chartData.datasets.push({
          label: newVal.chartTitle,
          backgroundColor: this.chooseColor(),
          data
        });
        
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
  