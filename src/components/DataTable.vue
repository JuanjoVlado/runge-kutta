<template>
    <div class="container">
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
                <tr v-for="row in tableData" :key="row[0]">
                    <td v-for="val in row" :key="val">{{val}}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>

export default {
    name: 'DataTable',
    props: {
        tableData: Array
    },
    data() {
        return {}
    }
}

</script>

<style scoped>
.container {
    overflow: scroll;
}
table {
    width: 100%;
    border-collapse: collapse;
}

th, td {
    border: 1px solid #8f8f8f;
}
</style>