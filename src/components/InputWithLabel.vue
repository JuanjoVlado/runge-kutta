<template>
    <div class="settings-container"
        :label="labelValue"
        :type="type"
        :value="defaultValue">
        <div class="input-with-label">
            <label for="function_input">{{ labelValue }}</label>
            <input id="function_input"
            :type="type"
            :value="defaultValue">
        </div>
    </div>
</template>

<script>

export default {
    name: 'GraphSettings',
    components: {
    },
    props: {
        labelValue: String,
        type: String,
        defaultValue: [String, Number]
    },
    data() {
        return {}
    }
}

</script>

<style scoped>
.settings-container {
    padding: 0.1em 0.5em;
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