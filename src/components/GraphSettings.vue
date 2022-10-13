<template>
    <div class="settings-container">
        <div>
            <input-with-label :label-value="`y' = `" :type="'text'"/>
        </div>
        <div class="two-cols">
            <div>
                <input-with-label :label-value="`h = `" :type="'number'" :default-value="0.1"/>
                <input-with-label :label-value="`xi = `" :type="'number'" :default-value="0"/>
                <input-with-label :label-value="`yi = `" :type="'number'" :default-value="0"/>
            </div>
            <div>
                <button>Aproximar</button>
            </div>
        </div>
    </div>
</template>

<script>
import InputWithLabel from './InputWithLabel.vue';

export default {
    name: 'GraphSettings',
    components: {
        InputWithLabel
    },
    props: {

    },
    data() {
        return {}
    }
}
</script>

<style scoped>
.settings-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    padding: 0;
}

.two-cols {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    padding: 0.5em 0;
}

.two-cols input {
    width: 7em; 
}

button {
    font-size: 20px;
    border: none;
    padding: 0.5em;
    margin-top: 0.2em;
    background-color: #05668D;
    color: white;
}

@media screen and (min-width: 768px) {
    .two-cols {
        flex-direction: column;
    }
    .settings-container {
        padding-right: 0.5em;
    }
}
</style>
