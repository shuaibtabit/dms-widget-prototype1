<template>
    <div class="dms-widget">
      <h2>Mutation Effect Search</h2>
  
      <!-- Dropdown to select mutation type -->
      <select v-model="selectedMutationType">
        <option disabled value="">Select Mutation Type</option>
        <option value="h3_mutation">H3 Mutation</option>
        <option value="h5_mutation">H5 Mutation</option>
        <option value="sequential_mutation">Sequential Mutation</option>
      </select>
  
      <!-- Input for entering mutation query -->
      <input type="text" v-model="query" placeholder="Enter mutation (e.g., M-6A)" />
      <button @click="searchMutation">Search</button>
  
      <div v-if="loading">Loading...</div>
      <div v-if="error">{{ error }}</div>
  
      <div v-if="results && results.length">
        <h3>Results for {{ query }} ({{ selectedMutationType }})</h3>
        <!-- Display results here -->
        <div v-for="(result, index) in results" :key="index">
          <pre>{{ result }}</pre>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        query: '', // User input for mutation
        selectedMutationType: '', // Type of mutation to search for
        results: null, // Search results
        loading: false, // Loading state
        error: null, // Error state
      };
    },
    methods: {
      async searchMutation() {
        if (!this.selectedMutationType) {
          this.error = 'Please select a mutation type to search.';
          return;
        }
  
        this.loading = true;
        this.error = null;
        this.results = null;
  
        try {
          // Fetch mock data or replace with actual data source URL
          const response = await fetch('/mockData.json');
          const data = await response.json();
  
          // Filter results based on the user's query and selected mutation type
          this.results = data.filter(item =>
            item[this.selectedMutationType]?.toLowerCase().includes(this.query.toLowerCase())
          );
  
          if (this.results.length === 0) {
            this.error = 'No results found for the given mutation.';
          }
  
        } catch (err) {
          this.error = 'An error occurred while fetching data.';
        } finally {
          this.loading = false;
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .dms-widget {
    max-width: 600px;
    margin: auto;
    text-align: center;
  }
  select,
  input {
    margin: 10px;
    padding: 8px;
  }
  button {
    padding: 8px 16px;
    margin-left: 10px;
  }
  </style>
  