<template>
    <div>
      <!-- Dropdown to select mutation type -->
      <select v-model="selectedMutationType">
        <option disabled value="">Select Mutation Type</option>
        <option v-for="option in mutationOptions" :key="option" :value="option">
          {{ option }}
        </option>
      </select>
  
      <!-- Input for entering mutation query -->
      <input v-model="query" placeholder="Search mutations..." />
      <button @click="handleSearch">Search</button>
      <div>
    <div class="table-container">
      <div class="table-header">
        <button @click="toggleTableSize">{{ isCollapsed?'Max':'Min' }}</button>
      </div>
      <div class="resizeable-table" :class="{collapsed:isCollapsed}">
        <table>
        <thead>
            <tr>
                <th v-for="header in headers" :key="header">{{ header }}</th>
            </tr>
        </thead>
        <tbody>
          <tr v-for="(row, index) in paginatedResults" :key="index">
            <td v-for="header in headers" :key="header" >{{ row[header] }}</td>
          </tr>
        </tbody>
      </table>
      </div>
      <div class="pagination">
        <button @click="changePage(currentPage-1)" :disabled="currentPage===1">Previous</button>
        <span> Page {{ currentPage }} of {{ totalPages }} </span>
        <button @click="changePage(currentPage+1)" :disabled="currentPage===totalPages">Next</button>
      </div>
    </div>
  </div>
  </div>
</template>
  
  <script>
  export default {
    props: {
      csvLink: String, // CSV link passed as a prop
      mutationType: String // Comma-separated mutation types
    },
    data() {
      return {
        headers : [],
        query: '', // User search query
        dataset: [], // Parsed dataset from CSV
        selectedMutationType: '', // Type of mutation selected
        results: [], // Stores the search results
        itemsPerPage : 10,
        currentPage : 1,
        isCollapsed: false
      };
    },
    mounted() {
      this.fetchCSV(); // Fetch the CSV file on mount
    },
    computed: {
      mutationOptions() {
        // Split mutationType prop (comma-separated string) into an array
        return this.mutationType ? this.mutationType.split(",") : [];
      },
      filteredResults() {
        // If no query or mutation type is selected, return the full dataset
        if (!this.query || !this.selectedMutationType) return this.dataset;
  
        // Filter the dataset based on the query and selected mutation type
        return this.dataset.filter(row => {
          if (!row[this.selectedMutationType]) return false;
          return row[this.selectedMutationType].toLowerCase() === this.query.toLowerCase();
        });
      },
      totalPages(){
        return Math.ceil(this.results.length/this.itemsPerPage);
      },
      paginatedResults(){
        let start = (this.currentPage-1) * this.itemsPerPage;
        let end = this.currentPage * this.itemsPerPage
        return this.results.slice(start,end);
      }
    },
    methods: {
      handleSearch() {
        // Update the results based on the filtered results
        this.results = this.filteredResults;
  
        if (!this.results.length) {
          this.error = `No results found for "${this.query}" in ${this.selectedMutationType}.`;
        } else {
          this.error = null;
        }
      },
      fetchCSV() {
        if (this.csvLink) {
          this.loading = true;
          fetch(this.csvLink)
            .then(response => response.text()) // Fetch CSV as plain text
            .then(csvData => this.parseCSV(csvData)) // Parse CSV data
            .catch(error => {
              console.error(error);
              this.error = "Error fetching CSV data.";
            })
            .finally(() => {
              this.loading = false;
            });
        }
      },
      parseCSV(csvData) {
        const rows = csvData.split("\n");
        this.headers = rows[0].split(",").map(header => header.trim()); // Split and trim headers
        const data = rows.slice(1).map(row => {
          const values = row.split(",");
          const obj = {};
          this.headers.forEach((header, index) => {
            obj[header] = values[index] ? values[index].trim() : '';
          });
          return obj;
        });
  
        this.dataset = data;
        this.results = data; // Set parsed dataset
      },
      toggleTableSize(){
        this.isCollapsed = !this.isCollapsed;
      },
      changePage(page){
        if(page>=1 && page<this.totalPages){
          this.currentPage = page;
        }
      }
    }
  };
  </script>
  
  <style scoped>
  /* Container for the table */
.table-container {
  position: relative;
  max-width: 100%;
  margin: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  background-color: #f9f9f9;
  overflow: hidden;
}

/* Header for the table controls */
.table-header {
  display: flex;
  justify-content: flex-end;
  margin-bottom: 10px;
}

/* Button to collapse and expand the table */
.table-header button {
  padding: 6px 12px;
  font-size: 14px;
  cursor: pointer;
}

/* Resizable and scrollable table */
.resizable-table {
  max-height: 400px;
  overflow-y: auto;
  overflow-x: hidden;
  resize: both;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: white;
  transition: all 0.3s ease;
}

.resizable-table.collapsed {
  max-height: 50px;
  overflow: hidden;
}

/* Table base styling */
table {
  width: 100%;
  border-collapse: collapse;
  margin: 0;
  font-size: 14px;
  text-align: left;
}

th, td {
  padding: 12px 15px;
  border: 1px solid #ddd;
}

/* Sorting arrows and clickable headers */
th {
  cursor: pointer;
  background-color: #009879;
  color: white;
}

/* Hover effect for rows */
tbody tr:hover {
  background-color: #f1f1f1;
  cursor: pointer;
}

/* Pagination controls */
.pagination {
  margin-top: 10px;
  text-align: center;
}

.pagination button {
  padding: 8px 12px;
  margin: 0 5px;
  background-color: #009879;
  color: white;
  border: none;
  cursor: pointer;
}

.pagination button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
  