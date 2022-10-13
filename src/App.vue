<template>
  <div id="app">
    <div class="padding">
      <h1>Método de Runge-Kutta</h1>
      <graph-box/>
      <span class="separator"></span>
      <graph-settings id="graph_settings"/>
      <span class="separator"></span>
      <data-table id="data_table" :table-data="tableData"/>
      <span class="separator"></span>
    </div>
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
      tableData: [
        [1.12345678,2.12345678,3.12345678,4.12345678,5.12345678,16.12345678],
        [6.12345678,7.12345678,8.12345678,9.12345678,0.12345678,17.12345678],
        [11.12345678,12.12345678,13.12345678,14.12345678,15.12345678,18.12345678]
      ]
    }
  }
}
</script>

<style>
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

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 0;
  height: 100%;
}

.separator {
  display: block;
  height: 2em;
}

.padding {
  padding: 0.75em;
}

@media screen and (min-width: 768px) {
  .separator {
    display: none;
  }

  h1 {
    grid-column-start: 1;
    grid-row-start: 1;
    grid-column-end: 3;
  }

  .padding {
    display: grid;
    grid-template-columns: 0.3fr 1fr;
    grid-template-rows: 0.1fr 1fr auto;
    justify-content: space-evenly;
    align-items: stretch;
  }

  #graph_settings {
    grid-column-start: 1;
    grid-row-start: 2;
  }

  #data_table {
    margin-top: 2em;
    grid-column-start: 1;
    grid-column-end: 3;
  }

  #app_footer {
    grid-row-start: 2;
  }
}
</style>
