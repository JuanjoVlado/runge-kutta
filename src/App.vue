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
          <input-with-label :label-value="'Limite x:'" :key-value="'xlim'" :input-value="xLimit" @input="updateXLimit"/>
          <div class="tab" v-show="activeTab==1">
            <span class="info">Ingrese EDO para aproximar:</span>
            <fn-input-with-label :label-value="`y' =`" :key-value="'fn'" :input-value="aproxSettings.fn" @input="updateAproxSettings"/>
            <div class="tab-input-grid">
              <input-with-label class="col-1" :label-value="'xi ='" :key-value="'x'" :input-value="aproxSettings.x" @input="updateAproxSettings"/>
              <input-with-label class="col-3" :label-value="'h ='" :key-value="'h'" :input-value="aproxSettings.h" @input="updateAproxSettings"/>
              <input-with-label class="col-1" :label-value="'yi ='" :key-value="'y'" :input-value="aproxSettings.y" @input="updateAproxSettings"/>
            </div>
            
            <div class="examples-container">
              <select name="examples" v-model="currentExample">
                <optgroup label="Examples">
                  <option value="example_01">Ejemplo 1</option>
                  <option value="example_02">Ejemplo 2</option>
                  <option value="example_03">Ejemplo 3</option>
                </optgroup>
              </select>
              <button @click="parseExpression(true)">Aproximar</button>
            </div>
          </div>
          <div class="tab" v-show="activeTab==2">
            <span class="info">Ingrese función para graficar:</span>
            <fn-input-with-label :label-value="`y =`" :key-value="'fn'" :input-value="fnPlotSettings.fn" @input="updateFnPlotSettings"/>
            <div class="tab-input-grid">
              <input-with-label :label-value="'x ='" :key-value="'x'" :input-value="fnPlotSettings.x" @input="updateFnPlotSettings"/>
            </div>
            <div class="examples-container">
              <select name="examples" v-model="currentExample">
                <optgroup label="Examples">
                  <option value="example_01">Ejemplo 1</option>
                  <option value="example_02">Ejemplo 2</option>
                  <option value="example_03">Ejemplo 3</option>
                </optgroup>
              </select>
              <button @click="parseExpression(false)">Graficar</button>
            </div>
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
      xLimit: 2,
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
          'solution': 'tan(x)'
        },
        'example_03':{
          'edo': {
            'fn':'-2*y+x^(3)*e^(-2*x)',
            'h': 0.1,
            'x': 0,
            'y': 1
          },
          'solution': '((e^(-2*x))/4)*(x^(4)+4)'
        }
      }
    }
  },
  methods: {
    // These methods handle the data when the user interacts with the app,
    // these have nothing to do with the proyect's objectives.
    updateXLimit(k, v) {
      this.xLimit = Number.parseInt(v);
    },
    updateAproxSettings(k, v) {
      let numval = Number.parseFloat(v*100)/100;
      this.aproxSettings[k] = isNaN(numval) ? v : numval;
      if(k == 'h') {
        this.fnPlotSettings[k] = isNaN(numval) ? v : numval;
      }
      if(k=='x') {
        this.xLimit = numval+1;
      }
    },
    updateFnPlotSettings(k, v) {
      let numval = Number.parseFloat(v*100)/100;
      this.fnPlotSettings[k] = isNaN(numval) ? v : numval;
      if(k=='x') {
        this.xLimit = numval+1;
      }
    },
    /**
     * Implementation of the Runge-Kutta method.
     * @param {function}  fn  Differencial equation to approximate.
     * @param {Number}    h   Size of the increment in x on each iteration.
     * @param {Number}    xi  Initial value for x.
     * @param {Number}    yi  Initial value for y. 
     * 
     * @returns {Object} The results of each step of the algorith for specific values of x and y.
     * The result object includes {k1, k2, k3, k4, y}.
     */
    runge_kutta(fn, h, xi, yi) {
      let k1 = fn.call(this, xi, yi);
      let k2 = fn.call(this, xi + (h/2), yi + (h*k1)/2);
      let k3 = fn.call(this, xi + (h/2), yi + (h*k2)/2);
      let k4 = fn.call(this, xi + h, yi + (h*k3));
      let y = (yi + (h/6)*(k1 + (2*k2) + (2*k3) + k4));

      return {k1, k2, k3, k4, y};
    },
    /**
     * This method executes the Runge-Kutta method for each value of x beginin with
     * xi up to a limit deffinden by the user. 
     * @param {function}  fn  Differencial equation to approximate.
     * @param {Number}    h   Size of the increment in x on each iteration.
     * @param {Number}    xi  Initial value for x.
     * @param {Number}    yi  Initial value for y.
     * 
     * @returns {Array} List of objects that represent the result of each step of
     * the Runge-Kutta algorithm.
     */
    getValues(fn, h, xi, yi) {
      let steps = [];
      let step = xi;
      let limit = this.xLimit;
      h = Math.round(Number.parseFloat(h)*100,2)/100;
      xi = Math.round(Number.parseFloat(xi)*100, 2)/100;
      yi = Number.parseFloat(yi);

      while(step <= limit) {
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
    /**
     * Evaluates an string that represents a mathematical exression, captures each instance
     * of known mathematical functions and returns the result of operate them.
     * This method can operate square roots, natural logarithm, cos, sin, tan, and euler's constant
     * powered to a value.
     * 
     * i.e.: sqrt(5) is replaced by '5'.
     * 
     * @param {String} s The mathematical expression to evaluate.
     * 
     * @returns {String} A new string that represent the mathematical expression after replacing the
     * functions by the result of operating them.
     */
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
    /**
     * Evaluates an string that represents a mathematical exression, captures each instance
     * of a number powered to a number and returns a new string replacing said expression by
     * the result of operating it.
     * 
     * i.e.: 2^5 is replaced by 32.
     *  
     * @param {String} The mathematical expression to evaluate.
     * 
     * @returns {String} A new string that represent the mathematical expression after replacing the
     * powers by the result of operating them.
     */
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
    /**
     * Evaluates an string that represents a mathematical exression, captures each instance
     * of a product or division between two numbers and returns a new string replacing said
     * expression by the result of operating it.
     * 
     * @param {String} s The mathematical expression to evaluate.
     * @param {Boolean} product Whether or not to operate products. If set to false,
     * divisions will be operated instead of products.
     * 
     * @returns {String} A new string that represent the mathematical expression after operating
     * products or divisions.
     */
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
    /**
     * Recursively captures and operates the expressions inside parentheses, from the inner
     * groups and to the outside, replacing the original expression by the result of operating
     * what's inside parentheses. 
     * 
     * @param {String} s The mathematical expression to evaluate.
     * 
     * @returns {String} A new string that represent the mathematical expression after operating
     * what's inside all parentheses.
     */
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
    /**
     * Captures and operates additions and subtractions from a mathematical expression.
     * 
     * @param {String} s The mathematical expression to evaluate.
     * 
     * @returns {String} A new string that represent the mathematical expression after operating
     * additions and subtractions.
     */
    operateAdditionsSubtractions(s) {
      let match = s.match(/([+-]?[\d.]+)/g);
      if(!match) return s;
      let res = 0;
      
      for(let n of match) {
        res += Number.parseFloat(n);
      }
      
      return res;
    },
    /**
     * Evaluates a mathematical expression and returns the result.
     * 
     * @param {String} exp The mathematical expression to evaluate.
     * 
     * @returns {String} The result of operating the mathematical expression.
     */
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
    /**
     * Transforms an string represinting a valid matematical expression and returns
     * a function that will evaluate the functions for a pair of (x, y) values.
     * 
     * @param {String} mathExpression The mathematical expression to evaluate.
     * 
     * @returns {function} Actual javascript function that evaluates the mathExpression for
     * a pair of (x, y) values.
     */
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
    /**
     * Evaluates a function from x to a limit deffined by the user adding to x an amount
     * equal to h for each iteration.
     * 
     * @param {function} fn Function to plot.
     * @param {Number} h Size of each step betwen plot values.
     * @param {Number} x Initial value for x.
     * 
     * @returns {Array} List of objects that represent the result of evaluating the function
     * for all points. The format of the input allow using the same function to plot the aproximation
     * generated by the Runge-Kutta method.
     */
    plotSolution(fn, h, x) {
      let data = []
      for(let i = x; i <= this.xLimit; i+=h) {
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
    /**
     * Starts the process of building a function from the mathematical expression
     * provided by the user, evaluate for (x, y) values and plot the results.
     * 
     * @param {boolean} toAproximate Indicates if the function to plot is
     * the aproximation of the differencial equation or it's soltion provided
     * by the user.
     */
    parseExpression(toAproximate) {
      let expression = toAproximate ? this.aproxSettings.fn : this.fnPlotSettings.fn;
      let fn = this.functionBuilder(expression);

      if(toAproximate) {
        let s = this.aproxSettings;
        this.tableData = {
          'chartTitle': 'A: '+this.aproxSettings.fn,
          'data': this.getValues(fn, s.h, s.x, s.y)
        }
      } else {
        this.tableData = {
          'chartTitle': 'G: '+this.fnPlotSettings.fn,
          'data': this.plotSolution(fn, this.fnPlotSettings.h, this.fnPlotSettings.x)
        }
      }
    }
  },
  // Not related to the proyect's objectives.
  watch: {
    currentExample(newVal) {
      let newObj = this.examples[newVal].edo;
      this.aproxSettings = newObj;
      this.fnPlotSettings.fn = this.examples[newVal].solution;
      this.fnPlotSettings.h = newObj.h;
      this.fnPlotSettings.x = newObj.x;
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
.tab-input-grid {
  display: grid;
  grid-template-columns: 1fr 0.2fr 1fr;
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
input {
  padding-left: 0.2em;
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

.col-1 {
  grid-column-start: 1;
}
.col-3 {
  grid-column-start: 3;
}

@media screen and (min-width: 768px) {
  .divider {display: none;}
  
  body,
  #app {
    display: grid;
    justify-content: center;
    background-color: #dbd9d3;
  }

  #app {
    grid-template-columns: min-content;
    background-color: white;;
  }
  .work-area {
    display: grid;
    grid-template-columns: min-content 1f;
    width: 45em;
    padding: 0.3em 1em;
  }
  h1 {
    grid-column: 1 / span 2;
  }
  #chart_display {
    grid-column-start: 2;
  }

  .settings-container {
    grid-column-start: 1;
    grid-row-start: 2;
    margin-right: 1em;
    min-width: 13em;
  }

  #data_table {
    grid-column: 1 / span 2;
  }
}
</style>
