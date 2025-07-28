<template>
    <div>
      <div v-if="loading">Loading...</div>
      <div v-else>
        <table class="table-auto w-full border">
          <thead class="bg-gray-100">
            <tr>
              <th class="px-4 py-2 border">#</th>
              <th class="px-4 py-2 border">Temperature (°C)</th>
              <th class="px-4 py-2 border">Vibration</th>
              <th class="px-4 py-2 border">Created At</th>
              <th class="px-4 py-2 border">Status</th>
            </tr>
          </thead>
          <tbody>
  
            <tr v-for="(item, index) in paginatedData" :key="item.id">
                <td class="px-4 py-2 border">{{ index + 1 + (currentPage - 1) * pageSize }}</td>
                <td class="px-4 py-2 border">{{ Number(item.temperature).toFixed(2) }}</td>
                <td class="px-4 py-2 border">{{ Number(item.vibration).toFixed(2) }}</td>
                <td class="px-4 py-2 border">{{ formatDate(item.created_at) }}</td>
                <td class="px-4 py-2 border">
                <span
                    :class="{
                    'text-green-600 font-bold': getStatus(item) === 'Healthy',
                    'text-yellow-600 font-bold': getStatus(item) === 'Warning',
                    'text-red-600 font-bold': getStatus(item) === 'Failing',
                    'text-gray-600 font-bold': getStatus(item) === 'Unknown'
                    }"
                >
                    {{ getStatus(item) }}
                </span>
                </td>
            </tr>
            </tbody>

        </table>
  
        <!-- No data message -->
        <div v-if="storedData.length === 0" class="text-center mt-4 text-gray-500">
          No data available.
        </div>
  
        <!-- Pagination Controls -->
        <div v-if="totalPages > 1" class="mt-4 flex justify-center space-x-2">
          <button
            @click="currentPage--"
            :disabled="currentPage === 1"
            class="px-3 py-1 border rounded bg-gray-200 hover:bg-gray-300"
          >
            Prev
          </button>
  
          <span class="px-3 py-1">{{ currentPage }} / {{ totalPages }}</span>
  
          <button
            @click="currentPage++"
            :disabled="currentPage === totalPages"
            class="px-3 py-1 border rounded bg-gray-200 hover:bg-gray-300"
          >
            Next
          </button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  import { laravelApiUrl } from '../../api';
  
  export default {
    name: "ManagerRecentHistory",
    data() {
      return {
        storedData: [],
        loading: true,
        currentPage: 1,
        pageSize: 10,
      };
    },
    computed: {
      totalPages() {
        return Math.ceil(this.storedData.length / this.pageSize);
      },
      paginatedData() {
        const start = (this.currentPage - 1) * this.pageSize;
        return this.storedData.slice(start, start + this.pageSize);
      }
    },
    methods: {
      async fetchStoredData() {
        try {
          const response = await axios.get(`${laravelApiUrl}/user/fetch_stored_data`, {
            headers: {
              'Authorization': `Bearer ${localStorage.getItem('auth_token')}`,
              'Content-Type': 'application/json'
            }
          });
  
          this.storedData = Array.isArray(response.data.stored_data)
            ? response.data.stored_data
            : [];
  
          this.currentPage = 1; // Reset to first page
  
        } catch (error) {
          console.error("Failed to fetch stored data:", error);
          this.storedData = [];
        } finally {
          this.loading = false;
        }
      },
  
      formatDate(dateString) {
        const date = new Date(dateString);
        return date.toLocaleString();
      },
  
      getStatus(item) {
        const temp = parseFloat(item.temperature);
        const vib = parseFloat(item.vibration);
  
        if (temp > 100 || vib > 4.5) {
          return "Failing";
        } else if ((temp >= 76 && temp <= 100) || (vib > 2.0 && vib <= 4.5)) {
          return "Warning";
        } else if ((temp >= 55 && temp <= 75) && (vib >= 0.5 && vib <= 2.0)) {
          return "Healthy";
        } else {
          return "Unknown";
        }
      },
    },
    mounted() {
      this.fetchStoredData();
    },
  };
  </script>
  
  <style scoped>
  table {
    border-collapse: collapse;
  }
  </style>
  