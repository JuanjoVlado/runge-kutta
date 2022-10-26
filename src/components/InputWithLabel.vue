<template>
    <div class="input-container">
        <div class="input-with-label">
            <label>{{ labelValue }}</label>
            <input type="number"
            :value="content"
            @input="parametersChanged">
        </div>
    </div>
</template>

<script>

export default {
    name: 'InputWithLabel',
    props: {
        labelValue: String,
        type: String,
        keyValue: String,
        inputValue: Number
    },
    data() {
        return {
            content: ""
        }
    },
    methods: {
        parametersChanged(event) {
            this.$emit('input', this.keyValue, event.target.value);
        }
    },
    watch: {
        inputValue(newVal) {
            this.content = newVal
        }
    },
    mounted() {
        this.content = this.inputValue;
    }
}

</script>

<style scoped>
.input-container {
    margin-bottom: 0.3em;
}

.input-with-label {
    display: flex;
    justify-content: left;
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
    width: 3em;
    border-radius: 0 0.2em 0.2em 0;
    border: 1px solid #8f8f9d;
    border-left: none;
    flex-grow: 1;
}

@media screen and (min-width: 768px) {
}
</style>