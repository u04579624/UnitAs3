<template>
  <div v-if="post && post.title" class="p-4 border rounded shadow hover:shadow-lg">
    <h2 class="text-xl font-bold">{{ post.title }}</h2>
    <p class="text-gray-600">By {{ post.author }}</p>
    <p>{{ post.snippit }}</p>
    <NuxtLink :to="'/posts/' + post.id" class="text-blue-500">
      Read More
    </NuxtLink>
  </div>
  <div v-else class="text-red-500">Invalid post data</div>
</template>

<script setup>
defineProps(['post']);
</script>