<template>
    <div class="input-container">
        <div class="input-with-label">
            <label for="function_input">{{ labelValue }}</label>
            <input id="function_input" type="text"
            :value="content"
            @input="parametersChanged">
        </div>
    </div>
</template>

<script>

export default {
    name: 'GraphSettings',
    data() {
        return {
            content: ""
        }
    },
    props: {
        labelValue: String,
        keyValue: String
    },
    methods: {
        parametersChanged(event) {
            this.$emit('input', this.keyValue, event.target.value);
        }
    },
    watch: {
        content(newVal) {
            let v = newVal;
            v = v.toLocaleLowerCase();
            v = v.replace(/pi/ig, 'PI');
            v = v.replace(/([xyI])([Pxy])/g, '$1*$2');
            v = v.replace(/(\d+|i)([pxy])/g, '$1*$2');
            
            if(v.endsWith('^') ||
               v.endsWith("sqrt") ||
               v.endsWith("ln") ||
               v.endsWith("sin") ||
               v.endsWith("sen") ||
               v.endsWith("cos") ||
               v.endsWith("tan")
            ) {
                v += "(";
            }

            if(!v.endsWith('se') && v.endsWith('e')) {
                v += '^(';
            }

            if(v.endsWith("**")) {
                v = v.replace('**', '^(');
            }

            this.content = v;
            this.$emit('parametersChanged', 'fn', v);
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