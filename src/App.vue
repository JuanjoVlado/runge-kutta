<template>
  <div id="app">
    <h1>Método de Runge-Kutta</h1>
    <graph-box id="chart_display" :table-data="tableData" @chartCleared="tableData={}" :chart-title="currentValues.fn"/>
    <graph-settings id="graph_settings"
      :current-values="currentValues"
      @exampleChanged="changeExample"
      @parametersChanged="updateTableData"/>
    <data-table id="data_table" :table-data="tableData"/>

    <app-footer id="app_footer"/>
  </div>
</template>

<script>
import GraphBox from './components/GraphBox.vue';
import GraphSettings from './components/GraphSettings.vue';
import DataTable from './components/DataTable.vue';
import AppFooter from './components/AppFooter.vue';

export default {
  name: 'App',
  components: {
    GraphBox,
    GraphSettings,
    DataTable,
    AppFooter
  },
  data() {
    return {
      tableData: {},
      currentValues: {
        "fn": "2*x*y",
        "h": 0.1,
        "x": 0,
        "y": 1
      },
      ruge_kutta(fn, h, xi, yi) {
        let k1 = fn(xi, yi);
        let k2 = fn(xi + (h/2), yi + (h*k1)/2);
        let k3 = fn(xi + (h/2), yi + (h*k2)/2);
        let k4 = fn(xi + h, yi + (h*k3));
        let y = (yi + (h/6)*(k1 + (2*k2) + (2*k3) + k4));

        return {k1, k2, k3, k4, y};
      },
      getValues(fn, h, xi, yi) {
          let steps = [];
          let step = 0;
          h = Number.parseFloat(h);
          xi = Number.parseFloat(xi);
          yi = Number.parseFloat(yi);

          while(step <= 1) {
            let res = this.ruge_kutta(fn, h, xi, yi);
            yi = res.y;
            res.x = Math.round(xi*100, 2)/100;
            res.key = step;
            steps.push(res)
            xi += h;
            step += h;
          }
          return steps;
      }
    }
  },
  methods: {
    changeExample(newValue) {
      switch (newValue) {
        case 'example_01':
          this.currentValues = {
            'fn': '2*x*y',
            'h': 0.1,
            'x': 1,
            'y': 5
          };
          break;
        case 'example_02':
          this.currentValues = {
            'fn': '1 + y^2',
            'h': 0.1,
            'x': 0,
            'y': 0
          };
          break;
        case 'example_03':
          this.currentValues = {
            'fn': 'x*y + sqrt(y)',
            'h': 0.1,
            'x': 1,
            'y': 0
          };
          break;
      }
    },
    // Transforma la expresión matemática en una función de javascript
    // y retorna dicha función para ser usada en otra parte.
    functionBuilder(mathExpression) {
      console.log("Original exp: "+mathExpression);
      let fn = function(x, y) {
        mathExpression = mathExpression.replace(/x/g, x);
        mathExpression = mathExpression.replace(/y/g, y);
        
        return (-2*y)+(Math.pow(x, 3)*Math.exp(-2*x));
      };

      return fn;
    },
    updateTableData(newValues, toAprox) {
      console.log(toAprox?'Aproximar: ':'Graficar: ', newValues.fn);
      let vals;
      if(toAprox) {
        vals = this.getValues(this.functionBuilder(newValues.fn), newValues.h, newValues.x, newValues.y);
      } else {
        // GRAFICAR
        vals = this.getValues(this.functionBuilder(newValues.fn), newValues.h, newValues.x, newValues.y);
      }
      this.tableData = vals;
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}

html,
body {
  margin: 0 !important;
  padding: 0;
  font-size: 24px;
  height: 100%;
}

h1 {
  font-size: 1.25em;
}

input, select {
  border: none;
  padding: 0;
  margin: 0;
}

button {
  font-size: 20px;
  border: none;
  padding: 0.5em;
  margin-top: 0.2em;
  background-color: #05668D;
  border: 1px solid #8f8f9d;
  color: white;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0;
  max-width: 50em;
}

@media screen and (min-width: 768px) {
  h1 {
    grid-column-start: 1;
    grid-row-start: 1;
    grid-column-end: 3;
  }

  #app {
    display: grid;
    grid-template-columns: 0.5fr 1fr;
    grid-template-rows: 0.1fr 1fr auto;
    justify-content: space-evenly;
    align-items: stretch;
  }
  #chart_display {
    grid-column: 2;
    grid-row: 2;
  }
  #graph_settings {
    grid-column-start: 1;
    grid-row-start: 2;
    margin-right: 1em;
  }

  #data_table {
    margin-top: 0;
    grid-column-start: 1;
    grid-column-end: 3;
  }

  #app_footer {
    grid-row-start: 4;
    grid-column: 1 / span 2;
  }
}
</style>
