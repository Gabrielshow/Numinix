<template>
  <div class="app">
    <div class="controls">
      <input v-model="searchQuery" type="text" placeholder="Search by name..." class="search-input" />
      <button @click="sortCustomers" class="sort-button">Sort Alphabetically</button>
    </div>

    <ul v-if="filteredCustomers.length > 0" class="customer-list">
      <li v-for="customer in filteredCustomers" :key="customer.id" class="customer-card">
        <img :src="customer.avatar" alt="Profile Image" class="profile-img" />
        <div class="customer-info">
          <h3>{{ customer.name }}</h3>
          <p>{{ customer.email }}</p>
        </div>
      </li>
    </ul>

    <!-- No Results Message -->
    <p v-else class="no-results">No customer(s) found with the search criteria.</p>
  </div>
</template>

<style scoped></style>

<script>
// import Card from './Card.vue'
import { ref, computed, onMounted } from "vue";
import { faker } from "@faker-js/faker";

export default {
  name: "App",
  setup() {
    const customers = ref([]);
    const searchQuery = ref("");
    const api = "https://jsonplaceholder.typicode.com/users"

    // Fetch customer data from JSONPlaceholder API
    const fetchCustomers = async () => {
      try {
        const response = await fetch(api);
        const data = await response.json();
        // Add fake profile images using Faker.js
        customers.value = data.map((customer) => ({
          ...customer,
          avatar: faker.image.avatar(),
        }));
      } catch (error) {
        console.error("Error fetching customer data:", error);
      }
    };

    // Filtered customers based on search query
    const filteredCustomers = computed(() => {
      return customers.value.filter((customer) =>
        customer.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    });

    // Sort customers alphabetically by name
    const sortCustomers = () => {
      customers.value.sort((a, b) => a.name.localeCompare(b.name));
    };

    // Fetch customers on mounted
    onMounted(fetchCustomers);

    return {
      customers,
      searchQuery,
      filteredCustomers,
      sortCustomers,
    };
  },
};


</script>
