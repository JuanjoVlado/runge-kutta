<template>
    <div class="table-container">
        <table>
            <thead>
                <tr>
                    <th>x</th>
                    <th>K1</th>
                    <th>K2</th>
                    <th>K3</th>
                    <th>K4</th>
                    <th>yi</th>
                    <th v-if="thisData.exact.length > 0">Valor real</th>
                    <th v-if="thisData.exact.length > 0">e%</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(row, k) in thisData.aprox" :key="row.step">
                    <td>{{row.x | roundVal}}</td>
                    <td>{{row.k1 | roundVal}}</td>
                    <td>{{row.k2 | roundVal}}</td>
                    <td>{{row.k3 | roundVal}}</td>
                    <td>{{row.k4 | roundVal}}</td>
                    <td>{{row.y | roundVal}}</td>
                    <td v-if="thisData.exact.length > 0">{{thisData.exact[k].y | roundVal}}</td>
                    <td v-if="thisData.exact.length > 0">{{calcError(thisData.exact[k].y,row.y)}}</td>
                </tr>
            </tbody>
        </table>
        <div class="empty"></div>
    </div>
</template>

<script>

export default {
    name: 'DataTable',
    data() {
        return {
            thisData: {
                'aprox': {},
                'exact': {}
            }
        }
    },
    props: {
        tableData: [Object, Array]
    },
    filters: {
        roundVal(val) {
            let roundFactor = 1000000;
            return Math.round(val*roundFactor, 6)/roundFactor;
        }
    },
    methods: {
        calcError(x, xi) {
            let absError = Math.abs(x-xi);
            let err = isNaN(absError/x) ? 0 : absError/x;
            return (Math.round(err*10000)/100)+'%';
        }
    },
    watch: {
        tableData(newValue) {
            if(newValue.aprox) {
                this.thisData.aprox = newValue.aprox;
            }

            if(newValue.exact) {
                this.thisData.exact = newValue.exact;
            }
        }
    }
}

</script>

<style scoped>
.table-container {
    overflow: scroll;
    display: flex;
    padding: 0.75em;
}
table {
    width: 100%;
    border-collapse: collapse;
}
th, td {
    border: 1px solid #8f8f8f;
    padding: 0.2em 0.4em;
}
</style>