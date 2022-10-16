<template>
    <div class="settings-container">
        <div class="tab-nav">
            <div class="tab-label" @click="activeTab=1" :class="{active : activeTab==1}">Aproximar</div>
            <div class="tab-label" @click="activeTab=2" :class="{active : activeTab==2}">Graficar</div> 
        </div>
        <div class="aprox-tab tab" v-if="activeTab==1">
            <span class="instructions">Ingrese EDO para aproximar:</span>
            <fn-input-with-label id="main_input"  :label-value="`y' =`" :currentValue="currentValues" :value-key="'fn'" @parametersChanged="parametersChanged"/>
            <input-with-label :label-value="`h =`" :type="'number'" :currentValue="currentValues" :value-key="'h'"  @parametersChanged="parametersChanged"/>
            <input-with-label :label-value="`xi =`" :type="'number'" :currentValue="currentValues" :value-key="'x'"  @parametersChanged="parametersChanged"/>
            <input-with-label :label-value="`yi =`" :type="'number'" :currentValue="currentValues" :value-key="'y'"  @parametersChanged="parametersChanged"/>
            <div class="controls">
                <select name="example_select" id="example_select" @change="exampleChanged">
                    <optgroup label="Ejemplos">
                        <option value="example_01">Ejemplo 1</option>
                        <option value="example_02">Ejemplo 2</option>
                        <option value="example_03">Ejemplo 3</option>
                    </optgroup>
                </select>
                <button id="aprox_input" @click="transmitNewParameters(true)">Aproximar</button>
            </div>
        </div>
        <div class="graph-tab tab" v-if="activeTab==2">
            <span class="instructions">Ingrese solución para graficar:</span>
            <fn-input-with-label :label-value="`y =`" :value-key="'fn'" @parametersChanged="parametersChanged"/>
            <button @click="transmitNewParameters(false)">Graficar</button>
        </div>
    </div>
</template>

<script>
import InputWithLabel from './InputWithLabel.vue';
import FnInputWithLabel from './FnInputWithLabel.vue';

export default {
    name: 'GraphSettings',
    components: {
        InputWithLabel,
        FnInputWithLabel
    },
    props: {
        currentValues: Object
    },
    data() {
        return {
            newValues: {"fn":"", "h":0, "x":0, "y":0},
            activeTab: 1,
            solutionValue: {"fn": "x^2 + 2x + b"}
        }
    },
    methods: {
        exampleChanged(event) {
            this.$emit('exampleChanged', event.target.value);
        },
        transmitNewParameters(toAprox) {
            this.$emit('parametersChanged', this.newValues, toAprox);
        },
        parametersChanged(key, newVal) {
            this.newValues[key] = newVal;
        }
    },
    mounted() {
        this.newValues = this.$props.currentValues;
    }
}
</script>

<style>
.tab {
    padding: 0.75em;
    margin: 0.75em;
    margin-top: 0;
    border: 1px solid #05668D;
    min-width: 14em;
}
.aprox-tab {
    display: grid;
    grid-template-columns: 1fr min-content;
    grid-template-rows: min-content 1fr;
}
.aprox-tab .instructions {
    grid-column: 1 / span 2;
}
.tab-nav {
    border-bottom: none;
    margin: 0 0.75em;
    margin-top: 1em;
    display: flex;
}
.tab-nav .tab-label {
    font-size: 0.8em;
    padding: 0.3em;
    background-color: #05668d69;
    color: #000000a3;
}
.tab-nav .tab-label:hover {
    cursor: pointer;
    user-select: none;
}
.tab-nav .tab-label.active {
    border: 1px solid #05668D;
    border-bottom: none;
    background-color: #05668D;
    color: white;
}
.controls select {
    font-size: 0.7em;
    border: 1px solid #8f8f9d;
    padding: 0.5em;
}
.instructions {
    display: block;
    font-size: 0.7em;
    text-align: left;
    padding: 0.4em 0;
}
#main_input {
    grid-row-start: 2;
    grid-column: 1 / span 2;
}
.input-container {
    grid-column: 1 / span 1;
}
.controls {
    grid-column-start: 2;
    grid-row: 3 / span 2;
    padding-left: 0.5em;
}
.controls * {
    display: block;
}

@media screen and (min-width: 768px) {
    .settings-container {
        padding: 0;
        grid-template-columns: 1fr;
        grid-template-rows: min-content 1fr;
    }
    .aprox-tab {
        grid-template-columns: 1fr min-content;
        grid-template-rows: min-content 1fr;
    }
}
</style>
