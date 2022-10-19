<template>
  <div id="app">
    <div class="work-area">  
      <h1>Método de Runge-Kutta</h1>
      <graph-box id="chart_display" :table-data="tableData" @chartCleared="tableData={}"/>
      <div class="divider"></div>
      <div class="settings-container">
        <div class="nav-bar">
          <div class="nav-tag" @click="activeTab=1" :class="{active:activeTab==1}">Aproximar</div>
          <div class="nav-tag" @click="activeTab=2" :class="{active:activeTab==2}">Graficar</div>
        </div>
        <div class="tabs-container">
          <div class="tab" v-show="activeTab==1">
            <span class="info">Ingrese EDO para aproximar:</span>
            <fn-input-with-label :label-value="`y' =`" :key-value="'fn'" :input-value="aproxSettings.fn" @input="updateAproxSettings"/>
            <input-with-label :label-value="'h ='" :key-value="'h'" :input-value="aproxSettings.h" @input="updateAproxSettings"/>
            <input-with-label :label-value="'xi ='" :key-value="'x'" :input-value="aproxSettings.x" @input="updateAproxSettings"/>
            <input-with-label :label-value="'yi ='" :key-value="'y'" :input-value="aproxSettings.y" @input="updateAproxSettings"/>
            <div class="examples-container">
              <select name="examples" id="examples_select" v-model="currentExample">
                <optgroup label="Examples">
                  <option value="example_01">Ejemplo 1</option>
                  <option value="example_02">Ejemplo 2</option>
                  <option value="example_03">Ejemplo 3</option>
                  <option value="example_04">Ejemplo 4</option>
                </optgroup>
              </select>
              <button @click="parseExpression(true)">Aproximar</button>
            </div>
          </div>
          <div class="tab" v-show="activeTab==2">
            <span class="info">Ingrese función para graficar:</span>
            <fn-input-with-label :label-value="`y =`" :key-value="'fn'" :input-value="fnPlotSettings.fn" @input="updateFnPlotSettings"/>
            <input-with-label :label-value="'x ='" :key-value="'x'" :input-value="fnPlotSettings.x" @input="updateFnPlotSettings"/>
            <button @click="parseExpression(false)">Graficar</button>
          </div>
        </div>
      </div>
      <div class="divider"></div>
      <data-table id="data_table" :table-data="tableData"/>
    </div>

    <app-footer id="app_footer"/>
  </div>
</template>

<script>
import GraphBox from './components/GraphBox.vue';
import DataTable from './components/DataTable.vue';
import AppFooter from './components/AppFooter.vue';
import InputWithLabel from './components/InputWithLabel.vue';
import FnInputWithLabel from './components/FnInputWithLabel.vue';

export default {
  name: 'App',
  components: {
    GraphBox,
    DataTable,
    AppFooter,
    FnInputWithLabel,
    InputWithLabel
  },
  data() {
    return {
      roundFactor: 1000000,
      decimalPoints: 6,
      activeTab: 1,
      currentExample: 'example_01',
      aproxSettings: {
        'fn': '2*x*y',
        'h': 0.1,
        'x': 1,
        'y': 2
      },
      fnPlotSettings: {
        'fn': 'e^(x^(2))',
        'h': 0.1,
        'x': 1,
      },
      mathExpression: "",
      tableData: {},
      examples: {
        'example_01': {
          'edo': {
            'fn': '2*x*y',
            'h': 0.1,
            'x': 1,
            'y': 5
          },
          'solution': 'e^(x^(2))'
        },
        'example_02': {
          'edo': {
            'fn': '1 + y^2',
            'h': 0.1,
            'x': 0,
            'y': 0
          },
          'solution': ''
        },
        'example_03': {
          'edo': {
            'fn': 'x*y + sqrt(y)',
            'h': 0.1,
            'x': 1,
            'y': 0
          },
          'solution': ''
        },
        'example_04':{
          'edo': {
            'fn':'x^2',
            'h': 0.1,
            'x': 0,
            'y': 0
          },
          'solution': 'x'
        }
      }
    }
  },
  methods: {
    updateAproxSettings(k, v) {
      this.aproxSettings[k] = v;
      if(k == 'h') {
        this.fnPlotSettings[k] = v;  
      }
    },
    updateFnPlotSettings(k, v) {
      this.fnPlotSettings[k] = v;
    },
    runge_kutta(fn, h, xi, yi) {
      let k1 = fn.call(this, xi, yi);
      let k2 = fn.call(this, xi + (h/2), yi + (h*k1)/2);
      let k3 = fn.call(this, xi + (h/2), yi + (h*k2)/2);
      let k4 = fn.call(this, xi + h, yi + (h*k3));
      let y = (yi + (h/6)*(k1 + (2*k2) + (2*k3) + k4));

      return {k1, k2, k3, k4, y};
    },
    getValues(fn, h, xi, yi) {
        let steps = [];
        let step = 0;
        h = Math.round(Number.parseFloat(h)*100,2)/100;
        xi = Math.round(Number.parseFloat(xi)*100, 2)/100;
        yi = Number.parseFloat(yi);

        while(step <= 2) {
          let res = this.runge_kutta(fn, h, xi, yi);
          yi = res.y;
          res.x = Math.round(xi*100, 2)/100;
          res.key = step;
          steps.push(res)
          xi += Math.round(h*100,2)/100;
          step += h;
        }

        return steps;
    },
    operateFunctions(s) {
      let matches = s.matchAll(/(?<fn>sqrt|log|sin|cos|tan|exp)\((?<arg>[^()]+)\)/g);
      
      if(matches){
        for(const match of matches) {
          let arg = this.operateExpression(match.groups.arg);
          // This rounding is necessary to avoid errors when operating PI with limited
          // decimal approximations.
          let opArg = Math.round(Math[match.groups.fn](arg)*1000000)/1000000;
          s = s.replace(match[0], opArg);
        }
      }
      
      return s;
    },
    operatePowers(s) {
      let matches = s.match(/(-?[\d.]+)\^(-?[\d.]+)/g);
      
      if(matches){
        for(const match of matches) {
          let nums = match.split('^');
          let m = Math.pow(Number.parseFloat(nums[0]), Number.parseFloat(nums[1]));
          s = s.replace(match, Math.round(m*this.roundFactor, this.decimalPoints)/this.roundFactor);
        }
      }
      return s.toString();
    },
    operateProductsAndQuotients(s, product) {
      let pattern = product ? /(-?[\d.]+)\*(-?[\d.]+)/ : /(-?[\d.]+)\/([-?\d.]+)/;
      let matches = s.match(pattern);
      while(matches){
        let arg01 = Number.parseFloat(matches[1]);
        let arg02 = Number.parseFloat(matches[2]);
        let operatedArg = product ? arg01*arg02 : arg01/arg02;
        s = s.replace(matches[0], operatedArg);
        matches = s.match(pattern);
      }
      return s;
    },
    operateParentheses(s) {
      let matches = s.match(/\([^()]+\)/);
      if(!matches) return s;
      
      while(matches) {
        for(let match of matches) {
          let parenthesesContent = match.replace(/\((.+)\)/, '$1');
          let m = this.operateExpression(parenthesesContent);
          s = s.replace(match, m);
        }
        matches = s.match(/\([^()]+\)/);
      }
      
      return s;
    },
    operateAdditionsSubtractions(s) {
      let match = s.match(/([+-]?[\d.]+)/g);
      if(!match) return s;
      let res = 0;
      
      for(let n of match) {
        res += Number.parseFloat(n);
      }
      
      return res;
    },
    operateExpression(exp) {
      exp = this.operateFunctions(exp);
      exp = this.operateParentheses(exp);
      exp = this.operatePowers(exp);
      exp = this.operateProductsAndQuotients(exp, true);
      exp = this.operateProductsAndQuotients(exp, false);
      exp = exp.replace(/\+-|-\+/g, '-');
      exp = this.operateAdditionsSubtractions(exp);
      return exp;
    },
    // Transforma la expresión matemática en una función de javascript
    // y retorna dicha función para ser usada en otra parte.
    functionBuilder(mathExpression) {
      this.mathExpression = mathExpression;

      let fn = function(x, y) {
        let exp = this.mathExpression;
        x = Math.round(x*100, 2)/100;

        exp = exp.replace(/x/g, x);
        exp = exp.replace(/y/g, y);
        exp = exp.replace(/PI/ig, Math.PI.toString());
        exp = exp.toLowerCase();
        exp = exp.replace(/(?<!\w)e(?!]w)/g, 'exp');
        exp = exp.replace(/\s+/g, '');
        exp = exp.replace(/exp/g, Math.exp(1));
        exp = exp.replace(/PI/ig, Math.PI.toString());
        exp = exp.replace(/ln/g, 'log');

        return this.operateExpression(exp);
      };

      return fn;
    },
    plotSolution(fn, h, x) {
      let data = []
      for(let i = x; i < (x+1); i+=h) {
        data.push({
          'x': Math.round(i*100,2)/100,
          'y': fn.call(this, i, 0),
          'k1': 0,
          'k2': 0,
          'k3': 0,
          'k4': 0,
          'key': i
        });
      }
      return data;
    },
    parseExpression(toAproximate) {
      console.log("parseExpression:", toAproximate)
      let expression = toAproximate ? this.aproxSettings.fn : this.plotSolution.fn;
      let fn = this.functionBuilder(expression);

      if(toAproximate) {
        console.log("Aproximate....");
      } else {
        this.tableData = this.plotSolution(fn, this.plotSolution.h, this.plotSolution.x);
      }
    },
    updateTableData() {

    }
  },
  watch: {
    currentExample(newVal) {
      let newObj = this.examples[newVal].edo;
      this.aproxSettings = newObj;
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

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0;
  max-width: 50em;
}
.divider {
  width: 100%;
  border-bottom: 1px solid #8f8f9d4f;
  margin-bottom: 1em;
}
.work-area {
  padding: 0.3em;
}

.nav-bar {
  display: flex;
  flex-direction: row;
}
.nav-bar .nav-tag {
  border: 1px solid #8f8f9d;
  border-bottom: none;
  border-left: none;
  font-size: 0.8em;
  padding: 0.4em;
}
.nav-bar .nav-tag:first-child {
  border-left: 1px solid #8f8f9d;;
}
.nav-tag.active {
  background-color: #05668D;
  color: white;
}
.nav-tag:hover {
  cursor: pointer;
  user-select: none;
}

.tabs-container {
  border: 1px solid #8f8f9d;
  padding: 0.5em;
  margin-bottom: 1em;
}
span.info {
  font-size: 0.6em;
  text-align: left;
  display: block;
  padding: 0.4em 0;
  padding-bottom: 0.6em;
}
input, select {
  border: none;
  padding: 0;
  margin: 0;
}

select {
  font-size: 0.8em;
  padding: 0.4em;
}

.examples-container {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
}

button {
  font-size: 20px;
  border: none;
  padding: 0.5em 0.8em;
  margin-top: 0.2em;
  background-color: #05668D;
  border: 1px solid #8f8f9d;
  color: white;
}
button:hover {
  cursor: pointer;
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
