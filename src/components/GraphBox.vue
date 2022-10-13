<template>
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
          labels: [
            '0.0',
            '0.1',
            '0.2',
            '0.3',
            '0.4',
            '0.5',
            '0.6',
            '0.7',
            '0.8',
            '0.9',
            '1.0'
          ],
          datasets: [
            {
              label: 'fn(x)',
              backgroundColor: '#f87979',
              data: [40, 39, 10, 40, 39, 80, 40]
            }
          ]
        },
        chartOptions: {
          responsive: true,
          maintainAspectRatio: false
        }
      }
    },
    methods: {
        test_function_01(x, y) {
            return (-2*y)+(Math.pow(x, 3)*Math.exp(-2*x));
        },
        ruge_kutta(f, h, xi, yi) {
            let k1 = f(xi, yi);
            let k2 = f(xi + (h/2), yi + (h*k1)/2);
            let k3 = f(xi + (h/2), yi + (h*k2)/2);
            let k4 = f(xi + h, yi + (h*k3));
            
            let y = (yi + (h/6)*(k1 + (2*k2) + (2*k3) + k4));
            
            return {k1, k2, k3, k4, y};
        },
        getValues(fn, h, xi, yi) {
            let steps = [];
            let step = 0;

            while(step <= 1) {
                let res = this.ruge_kutta(fn, h, xi, yi);
                yi = res.y;
                res.x = Math.round(xi*100, 2)/100;
                steps.push(res)
                xi += h;
                step += h;
            }
            
            return steps;
        }
    },
    mounted() {
        let values = this.getValues(this.test_function_01, 0.1, 0, 1);
        let nvals = values.map(n => n.y);
        this.chartData.datasets[0].data = nvals;
    }
  }



</script>
  