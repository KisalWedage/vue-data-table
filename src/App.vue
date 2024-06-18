<template>
  <div id="app">
    <DataTable :data="tableData" :headers="headers" />
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import DataTable from './components/DataTable.vue';

export default {
  name: 'App',
  components: {
    DataTable
  },
  setup() {
    const headers = ref(['id', 'name', 'email', 'body']);
    const tableData = ref([]);

    onMounted(async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/comments');
        const data = await response.json();
        tableData.value = data;
      } catch (error) {
        console.error('Failed to fetch data', error);
      }
    });

    return {
      headers,
      tableData
    };
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin-bottom: 60px;
}
</style>
