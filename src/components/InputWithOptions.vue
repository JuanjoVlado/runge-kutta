<template>
    <div class="settings-container"
        :label="labelValue"
        :type="type"
        :value="defaultValue"
        :select-name="selectName">

        <input-with-label class="input" 
            :label-value="labelValue" 
            :type="type" 
            :value="defaultValue"/>
        
        <div class="overlap">
            <label>
                <div class="arrow"></div>
                <select :name="selectName">
                    <option value="uno">uno</option>
                    <option value="dos">dos</option>
                    <option value="tres">tres</option>
                </select>
            </label>
        </div> 
    </div>
</template>

<script>
import InputWithLabel from './InputWithLabel.vue';

export default {
    name: 'InputWithOptions',
    components: {
        InputWithLabel
    },
    props: {
        labelValue: String,
        type: String,
        defaultValue: [String, Number],
        inputSize: Number,
        selectName: String
    },
    data() {
        return {}
    }
}

</script>

<style scoped>
.settings-container {
    position: relative;
}
.overlap {
    position: absolute;
    top: 0;
    right: 0.5em;
}
.overlap select {
    z-index: 2;
    background-color: transparent;
    color: transparent;
}
.overlap select:focus {
    border: none;
}
.overlap .arrow {
    position: absolute;
    border: 0.4em solid;
    border-color: transparent transparent black black;
    transform: rotate(-45deg);
    top: 0.35em;
    right: 0.6em;
    z-index: 1;
}
</style>