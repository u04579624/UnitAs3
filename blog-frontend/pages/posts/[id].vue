<template>
  <div class="container mx-auto px-4 py-12 max-w-3xl">
    <h1 v-if="post?.title" class="text-4xl font-bold text-gray-900 mb-4 leading-tight md:text-5xl">{{ post.title }}</h1>
    <h1 v-else class="text-4xl font-bold text-gray-900 mb-4 md:text-5xl">Post Not Found</h1>
    <div v-if="post?.author || post?.publishedAt" class="flex items-center text-gray-600 mb-6 space-x-2">
      <span v-if="post?.author" class="italic">By {{ post.author }}</span>
      <span v-if="post?.author && post?.publishedAt" class="text-gray-400">•</span>
      <span v-if="post?.publishedAt" class="text-sm">
        {{ new Date(post.publishedAt).toLocaleDateString('en-US', { year: 'numeric', month: 'long', day: 'numeric' }) }}
      </span>
    </div>
    <div v-if="post?.content" class="prose prose-lg max-w-none text-gray-800 leading-relaxed">
      <p v-for="block in post.content" :key="block.type">
        {{ block.children[0].text }}
      </p>
    </div>
    <p v-if="error" class="text-red-500 mt-6 font-medium">{{ error }} (Check Strapi API for post ID {{ route.params.id }})</p>
    <nuxt-link to="/" class="mt-8 inline-block text-blue-600 hover:text-blue-800 font-medium transition-colors">
      ← Back to Home
    </nuxt-link>
  </div>
</template>

<script setup>
import { useRoute } from 'vue-router';

const route = useRoute();
const error = ref(null);
const rawPostData = ref(null);
let data = ref(null);

const postId = parseInt(route.params.id);

// Validate only for numeric ID
if (isNaN(postId)) {
  console.error('Invalid ID detected:', route.params.id);
  error.value = 'Invalid post ID. Expected a number, got: ' + route.params.id;
} else {
  const fetchResult = await useFetch(`http://localhost:8080/api/blog-posts/${postId}`, {
    onRequest() {
      console.log('Fetching post for ID:', postId);
    },
    onRequestError(err) {
      console.error('Fetch request error:', err);
      error.value = err.message;
    },
    onResponse({ response }) {
      console.log('Fetch status:', response.status);
      console.log('Raw Post Response:', JSON.stringify(response._data, null, 2));
      rawPostData.value = response._data;
      if (!response._data?.data) {
        error.value = `Post ID ${postId} not found in Strapi`;
      }
    },
    onResponseError(err) {
      console.error('Fetch response error:', err);
      error.value = err.message;
    },
  });
  data = fetchResult.data;
}

const post = computed(() => {
  const postData = data.value?.data || {};
  console.log('Computed post:', JSON.stringify(postData, null, 2));
  return postData;
});
</script>