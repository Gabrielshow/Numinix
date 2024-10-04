<template>
  <v-for>
    <Card props={{props}} />
</template>

<style scoped></style>

<script setup>
import Card from './Card.vue'
import { ref, computed, onMounted } from "vue";
import { faker } from "@faker-js/faker";

const customers = ref([]);
const searchQuery = ref("");

api = "http://jsonplaceholder.typicode.com/users"
const fetchCustomers = async () => {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    const data = await response.json();
    customers.value = data.map((customer) => ({
      ...customer,
      avatar: faker.image.avatar(),
    }));
  } catch (error) {
    console.error("Error fetching customer data:", error);
  }
};

const filteredCustomers = computed(() => {
  return customers.value.filter((customer) =>
    customer.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});

const sortCustomers = () => {
  customers.value.sort((a, b) => a.name.localeCompare(b.name));
};

onMounted(fetchCustomers);

return {
  customers,
  searchQuery,
  filteredCustomers,
  sortCustomers,
};


</script>
