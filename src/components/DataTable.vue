<template>
    <div class="data-table-container">
      <input v-model="searchQuery" placeholder="Search here..." />
      <p class="sorting-heading">Click on the table headings to sort items</p>
      <table>
        <thead>
          <tr>
            <th v-for="header in headers" :key="header" @click="sortBy(header)">
              {{ header }}
              <span v-if="sortKey === header">
                {{ sortAsc ? '⬆️' : '⬇️' }}
              </span>
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in paginatedData" :key="row.id">
            <td v-for="header in headers" :key="header">{{ row[header] }}</td>
          </tr>
        </tbody>
      </table>
  
      <div class="pagination">
        <button @click="prevPage" :disabled="currentPage === 1">⬅️</button>
        <span>Page {{ currentPage }} of {{ totalPages }}</span>
        <button @click="nextPage" :disabled="currentPage === totalPages">➡️</button>
      </div>
    </div>
  </template>
  
  <script>
  import { ref, computed, watch } from 'vue';
  
  export default {
    name: 'DataTable',
    props: {
      data: Array,
      headers: Array
    },
    setup(props) {
      const searchQuery = ref('');
      const sortKey = ref('');
      const sortAsc = ref(true);
      const currentPage = ref(1);
      const pageSize = ref(10);
  
      const filteredData = computed(() => {
        let sortedData = [...props.data];
  
        if (sortKey.value) {
          sortedData.sort((a, b) => {
            if (a[sortKey.value] < b[sortKey.value]) return sortAsc.value ? -1 : 1;
            if (a[sortKey.value] > b[sortKey.value]) return sortAsc.value ? 1 : -1;
            return 0;
          });
        }
  
        if (searchQuery.value) {
          sortedData = sortedData.filter(row =>
            Object.values(row).some(val =>
              String(val).toLowerCase().includes(searchQuery.value.toLowerCase())
            )
          );
        }
  
        return sortedData;
      });
  
      const totalPages = computed(() => Math.ceil(filteredData.value.length / pageSize.value));
  
      const paginatedData = computed(() => {
        const start = (currentPage.value - 1) * pageSize.value;
        const end = start + pageSize.value;
        return filteredData.value.slice(start, end);
      });
  
      const prevPage = () => {
        if (currentPage.value > 1) currentPage.value--;
      };
  
      const nextPage = () => {
        if (currentPage.value < totalPages.value) currentPage.value++;
      };
  
      const sortBy = (key) => {
        if (sortKey.value === key) {
          sortAsc.value = !sortAsc.value;
        } else {
          sortKey.value = key;
          sortAsc.value = true;
        }
      };
  
      watch(searchQuery, () => {
        currentPage.value = 1;
      });
  
      return {
        searchQuery,
        paginatedData,
        sortKey,
        sortAsc,
        currentPage,
        totalPages,
        prevPage,
        nextPage,
        sortBy
      };
    }
  };
  </script>
  
  <style scoped>
  .data-table-container {
    width: 90vw;
    height: 100vh;
    padding: 20px;
    background-color: #B2BEB5;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    flex-grow: 1;
  }
  
  th, td {
    padding: 8px;
    text-align: left;
    border: 1px solid #ddd;
  }
  
  th {
    cursor: pointer;
  }
  
  input {
    margin-bottom: 10px;
    padding: 5px;
    width: 100%;
    box-sizing: border-box;
  }

  .sorting-heading{
    font-size: 20px;
    color:red;
    padding-bottom: 10px
  }
  
  .pagination {
    margin-top: 10px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  button {
    padding: 5px 10px;
    cursor: pointer;
  }
  
  button:disabled {
    cursor: not-allowed;
    opacity: 0.5;
  }
  </style>
  