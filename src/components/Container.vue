<template>
  <div class="app">
    <div class="controls">
      <input v-model="searchQuery" type="text" placeholder="Search by name..." class="search-input" />

      <!-- Sort Dropdown -->
      <select v-model="sortOption" @change="sortCustomers" class="sort-dropdown">
        <option value="default">Filter by name</option>
        <option value="asc">Name (A-Z)</option>
        <option value="desc">Name (Z-A)</option>
      </select>
    </div>

    <!-- Customer Cards -->
    <div v-if="filteredCustomers.length > 0" class="customer-grid">
      <div v-for="customer in filteredCustomers" :key="customer.id" class="customer-card">
        <!-- Profile Image -->
        <img :src="customer.avatar" alt="Profile Image" class="profile-img" />

        <!-- Customer Details -->
        <div class="customer-info">
          <h3>{{ customer.name }}</h3>
          <p>{{ customer.email }}</p>
          <p>{{ customer.phone }}</p>
          <p>{{ customer.address.street }}, {{ customer.address.city }}</p>
        </div>
      </div>
    </div>

    <!-- No Results Message -->
    <p v-else class="no-results">No customer(s) found with the search criteria.</p>
  </div>
</template>

<style scoped>
/* Container for the app */
.app {
  padding: 20px;
}

/* Input and button for search and sort controls */
.controls {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  position: relative;
}

/* Search Input Styling */
.search-input {
  padding: 8px;
  font-size: 16px;
  width: 200px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

/* Sort Dropdown Styling */
.sort-dropdown {
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
  min-width: 160px;
  /* Ensures the dropdown and search input are aligned */
}

/* Flex container for the customer cards */
.customer-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

/* Styling for each customer card */
.customer-card {
  width: 100%;
  max-width: 250px;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease-in-out;
}

/* Add a hover effect for the card */
.customer-card:hover {
  transform: scale(1.05);
}

/* Profile image styling */
.profile-img {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

/* Styling for the customer info inside the card */
.customer-info {
  padding: 16px;
  text-align: center;
}

.customer-info h3 {
  margin: 0;
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.customer-info p {
  margin: 4px 0;
  font-size: 14px;
  color: #555;
}

/* Responsive styles */
@media (max-width: 768px) {
  .customer-card {
    max-width: 100%;
  }

  .customer-grid {
    justify-content: flex-start;
  }
}

@media (min-width: 769px) {
  .customer-card {
    max-width: calc(25% - 20px);
    /* 4 cards per row */
  }

  /* Desktop specific search button styling */
  .search-input {
    width: 548px;
    height: 52px;
    position: absolute;
    top: 168px;
    left: 166px;
    gap: 3px;
    opacity: 0;
  }
}

.no-results {
  text-align: center;
  font-size: 16px;
  color: #888;
}
</style>



<script>
import { ref, computed, onMounted } from "vue";

export default {
  name: "App",
  setup() {
    const customers = ref([]);
    const searchQuery = ref("");
    const sortOption = ref("default"); // New state to manage the sort option
    const api = "https://jsonplaceholder.typicode.com/users";

    // Fetch customer data from JSONPlaceholder API
    const fetchCustomers = async () => {
      try {
        const response = await fetch(api);
        const data = await response.json();

        // Add fake profile images using Faker.js or another source
        customers.value = data.map((customer) => ({
          ...customer,
          avatar: `https://randomuser.me/api/portraits/men/${Math.floor(Math.random() * 100)}.jpg`,
        }));
      } catch (error) {
        console.error("Error fetching customer data:", error);
      }
    };

    // Filtered customers based on search query
    const filteredCustomers = computed(() => {
      return customers.value
        .filter((customer) =>
          customer.name.toLowerCase().includes(searchQuery.value.toLowerCase())
        )
        .sort((a, b) => {
          // Handle sorting based on the sortOption value
          if (sortOption.value === "asc") {
            return a.name.localeCompare(b.name);
          } else if (sortOption.value === "desc") {
            return b.name.localeCompare(a.name);
          }
          return 0; // No sorting if 'default' is selected
        });
    });

    // Fetch customers on mounted
    onMounted(fetchCustomers);

    return {
      customers,
      searchQuery,
      filteredCustomers,
      sortOption, // Bind the sort option to the dropdown
    };
  },
};
</script>
