<template>
  <div class="container mx-auto">
    <CategoryFilter v-model="selectedCategory" />
    <div v-if="error" class="text-red-500">
      Error loading posts: {{ error.message }}
    </div>
    <div v-else-if="!posts || !posts.data || posts.data.length === 0" class="text-gray-500">
      No posts found.
    </div>
    <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-4">
      <BlogCard v-for="post in filteredPosts" :key="post.id" :post="post" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
const selectedCategory = ref('');
const config = useRuntimeConfig();
const fetchUrl = computed(() => {
  const url = selectedCategory.value
    ? `/api/blog-posts?filters[category][$eq]=${selectedCategory.value}`
    : '/api/blog-posts';
  return `${config.public.strapiUrl || 'http://localhost:8080'}${url}`;
});
console.log('Fetching from:', fetchUrl.value);
const { data: posts, error } = await useFetch(fetchUrl, {
  server: false,
  headers: { 'Accept': 'application/json' },
  onResponse({ response }) {
    console.log('API Response:', JSON.stringify(response._data, null, 2));
  },
  onResponseError({ response }) {
    console.error('API Response Error:', response.status, response._data);
  },
});
const filteredPosts = computed(() => {
  const filtered = posts.value?.data || [];
  console.log('Filtered Posts:', JSON.stringify(filtered, null, 2));
  return filtered;
});
if (error.value) {
  console.error('Fetch Error:', error.value);
}
</script>