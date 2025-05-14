<template>
  <select v-model="selectedCategory" class="p-2 border rounded">
    <option value="">All Categories</option>
    <option v-for="category in categories" :key="category" :value="category">{{ category }}</option>
  </select>
</template>
<script setup>
import { ref } from 'vue';
const selectedCategory = defineModel();
const categories = ['Tech', 'Sport', 'Food', 'Lifestyle'];
</script>