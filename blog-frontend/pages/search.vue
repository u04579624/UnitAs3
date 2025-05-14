<template>
  <div class="container mx-auto">
    <input v-model="query" placeholder="Search by title or author" class="p-2 border rounded w-full mb-4" />
    <div v-if="error" class="text-red-500">Error: {{ error.message }}</div>
    <div v-else-if="!posts || !posts.data || posts.data.length === 0" class="text-gray-500">No results found.</div>
    <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <BlogCard v-for="post in filteredPosts" :key="post.id" :post="post" />
    </div>
</div>
  </template>

<script setup>
import { ref, computed } from 'vue';
const config = useRuntimeConfig();
const query = ref('');
const fetchUrl = computed(() => {
  const baseUrl = config.public.strapiUrl || 'http://localhost:8080';
  return query.value
    ? `${baseUrl}/api/blog-posts?filters[$or][0][title][$contains]=${encodeURIComponent(query.value)}&filters[$or][1][author][$contains]=${encodeURIComponent(query.value)}`
    : `${baseUrl}/api/blog-posts`;
});
console.log('Search Fetch URL:', fetchUrl.value);
const { data: posts, error } = await useFetch(fetchUrl, {
  server: false,
  headers: { 'Accept': 'application/json' },
  onResponse({ response }) {
    console.log('Search API Response:', JSON.stringify(response._data, null, 2));
  },
  onResponseError({ response }) {
    console.error('Search API Error:', response.status, response._data);
  },
});
const filteredPosts = computed(() => {
  const filtered = posts.value?.data || [];
  console.log('Filtered Search Posts:', JSON.stringify(filtered, null, 2));
  return filtered;
});
if (error.value) {
  console.error('Search Fetch Error:', error.value);
}
</script>