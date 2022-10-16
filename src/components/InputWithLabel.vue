<template>
    <div class="input-container"
        :label="labelValue"
        :type="type">
        <div class="input-with-label">
            <label for="function_input">{{ labelValue }}</label>
            <input id="function_input"
            :type="type"
            :value="currentValue[valueKey]"
            @change="parametersChanged">
        </div>
    </div>
</template>

<script>

export default {
    name: 'GraphSettings',
    props: {
        labelValue: String,
        type: String,
        currentValue: Object,
        valueKey: String
    },
    methods: {
        parametersChanged(event) {
            this.$emit('parametersChanged', this.valueKey, event.target.value);
        }
    }
}

</script>

<style scoped>
.input-container {
    margin-bottom: 0.3em;
}

.input-with-label {
    display: flex;
    justify-content: center;
}

.input-with-label label {
    border: 1px solid #8f8f9d;
    padding: 0.2em;
    padding-left: 0.4em;
    border-radius: 0.2em 0 0 0.2em;
    border-right: none;
    min-width: 2.25em;
    text-align: left;
    padding-right: 0;
}
.input-with-label input {
    font-size: 1em;
    width: 4em;
    border-radius: 0 0.2em 0.2em 0;
    border: 1px solid #8f8f9d;
    border-left: none;
    flex-grow: 1;
}

@media screen and (min-width: 768px) {
    .input-with-label input {
        width: 2em;
    }
}
</style>