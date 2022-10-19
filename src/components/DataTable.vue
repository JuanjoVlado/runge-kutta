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
                </tr>
            </thead>
            <tbody>
                <tr v-for="row in thisData" :key="row.step">
                    <td>{{row.x | roundVal}}</td>
                    <td>{{row.k1 | roundVal}}</td>
                    <td>{{row.k2 | roundVal}}</td>
                    <td>{{row.k3 | roundVal}}</td>
                    <td>{{row.k4 | roundVal}}</td>
                    <td>{{row.y | roundVal}}</td>
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
            thisData: {}
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
    watch: {
        tableData(newValue) {
            this.thisData = newValue.data;
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