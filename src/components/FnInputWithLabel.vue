<template>
    <div class="input-container" :label="labelValue">
        <div class="input-with-label">
            <label for="function_input">{{ labelValue }}</label>
            <input id="function_input" type="text"
            v-model="fnValue"
            @change="parametersChanged">
        </div>
    </div>
</template>

<script>

export default {
    name: 'GraphSettings',
    data() {
        return {
            fnValue: ""
        }
    },
    props: {
        labelValue: String,
        currentValue: Object
    },
    methods: {
        parametersChanged(event) {
            this.$emit('parametersChanged', 'fn', event.target.value);
        }
    },
    watch: {
        fnValue() {
            this.fnValue = this.fnValue.toLocaleLowerCase();
            this.fnValue = this.fnValue.replace(/pi/ig, 'PI');
            this.fnValue = this.fnValue.replace(/([xyI])([Pxy])/g, '$1*$2');
            this.fnValue = this.fnValue.replace(/(\d+|i)([pxy])/g, '$1*$2');
            
            if(this.fnValue.endsWith('^') ||
               this.fnValue.endsWith("sqrt") ||
               this.fnValue.endsWith("ln") ||
               this.fnValue.endsWith("sin") ||
               this.fnValue.endsWith("sen") ||
               this.fnValue.endsWith("cos") ||
               this.fnValue.endsWith("tan")
            ) {
                this.fnValue += "(";
            }

            if(!this.fnValue.endsWith('se') && this.fnValue.endsWith('e')) {
                this.fnValue += '^(';
            }

            if(this.fnValue.endsWith("**")) {
                this.fnValue = this.fnValue.replace('**', '^(');
            }

            this.$emit('parametersChanged', 'fn', this.fnValue);
        },
        currentValue() {
            if(this.currentValue && this.currentValue.fn.length > 0) {
                this.fnValue = this.currentValue.fn;
            }
        }
    },
    mounted() {
        if(this.currentValue && this.currentValue.fn.length > 0) {
            this.fnValue = this.currentValue.fn;
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