<template>
  <div class="app">
    <div class="controls">
      <!-- Search Input -->
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
          <!-- Name and Username -->
          <h3 class="name-username">{{ customer.name }}</h3>
          <p class="username">@{{ customer.username }}</p>
          <p class="description">"{{ customer.company.catchPhrase }}"</p>

          <div class="customer-contact">
            <div class="contact-item">
              <img src="https://img.icons8.com/ios/50/000000/email.png" alt="Email Icon" class="contact-icon" />
              <p>{{ customer.email }}</p>
            </div>
            <div class="contact-item">
              <img src="https://img.icons8.com/ios/50/000000/marker.png" alt="Location Icon" class="contact-icon" />
              <p>{{ customer.address.street }}, {{ customer.address.city }}</p>
            </div>
            <div class="contact-item">
              <img src="https://img.icons8.com/ios/50/000000/phone.png" alt="Phone Icon" class="contact-icon" />
              <p>{{ customer.phone }}</p>
            </div>
            <div class="contact-item">
              <img src="https://img.icons8.com/ios/50/000000/domain.png" alt="Website Icon" class="contact-icon" />
              <p>{{ customer.website }}</p>
            </div>
            <div class="contact-item">
              <img src="https://img.icons8.com/ios/50/000000/briefcase.png" alt="Company Icon" class="contact-icon" />
              <p>{{ customer.company.name }}</p>
            </div>
            <div class="contact-item">
              <img src="https://img.icons8.com/ios/50/000000/factory.png" alt="Factory Icon" class="contact-icon" />
              <p>{{ customer.company.bs }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- No Results Message -->
    <p v-else class="no-results">No customer(s) found with the search criteria.</p>
  </div>
</template>

<style scoped>
.app {
  padding: 20px;
}

.controls {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  position: relative;
  flex-wrap: wrap;
}

@media (max-width: 768px) {
  .controls {
    flex-direction: column;
    align-items: flex-start;
  }

  .sort-dropdown {
    align-self: flex-end;
    margin-top: 10px;
  }
}

@media (min-width: 769px) {
  .controls {
    justify-content: space-between;
    align-items: center;
  }
}

.search-input {
  padding: 8px;
  font-size: 16px;
  width: 200px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.sort-dropdown {
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
  min-width: 160px;
}

.customer-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
}

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

.customer-card:hover {
  transform: scale(1.05);
}

.profile-img {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.customer-info {
  padding: 16px;
  text-align: center;
}

.name-username {
  margin: 0;
  font-size: 18px;
  font-weight: bold;
  color: #333;
  text-align: left;
}

.username {
  margin-top: 4px;
  font-size: 14px;
  font-weight: normal;
  color: #666;
  text-align: left;
}

.description {
  margin-top: 8px;
  font-size: 14px;
  color: #51C5FF;
  text-align: left;
}

.customer-contact {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 16px;
}

.contact-item {
  display: flex;
  text-align: left;
  font-size: 16px;
  gap: 6px;
  font-size: 14px;
  color: #555;
  width: 100%;
}

.contact-icon {
  width: 18px;
  height: 18px;
  color: blue;
}

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

        customers.value = data.map((customer) => ({
          ...customer,
          avatar: `https://randomuser.me/api/portraits/men/${Math.floor(Math.random() * 100)}.jpg`,
        }));
      } catch (error) {
        console.error("Error fetching customer data:", error);
      }
    };

    const filteredCustomers = computed(() => {
      let filtered = customers.value.filter((customer) =>
        customer.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      );

      if (sortOption.value === "asc") {
        filtered = filtered.sort((a, b) => a.name.localeCompare(b.name));
      } else if (sortOption.value === "desc") {
        filtered = filtered.sort((a, b) => b.name.localeCompare(a.name));
      }

      return filtered;
    });

    onMounted(fetchCustomers);

    return {
      customers,
      searchQuery,
      filteredCustomers,
      sortOption,
    };
  },
};
</script>
