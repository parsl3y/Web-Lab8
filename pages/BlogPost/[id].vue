<!-- views/BlogPost.vue -->
<template>
  <div v-if="post" class="post-details">
    <h3>Деталі поста</h3>
    <p><strong>ID:</strong> {{ post.id }}</p>
    <p><strong>Автор:</strong> {{ post.user.name }}</p>
    <p><strong>Категорія:</strong> {{ post.category.title }}</p>
    <p><strong>Заголовок:</strong> {{ post.title }}</p>
    <p><strong>Дата публікації:</strong> {{ post.published_at }}</p>
    <p><strong>Контент:</strong> {{ post.content_raw }}</p>
  </div>
  <div v-else>
    <p>Loading...</p>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue';
import { useRoute } from 'vue-router';

const post = ref(null);
const route = useRoute();

const getPostDetails = async (id) => {
  try {
    const response = await fetch(`http://127.0.0.1:8000/api/blog/posts/${id}`);
    if (response.ok) {
      const data = await response.json();
      post.value = data;
    } else {
      throw new Error('Failed to fetch post details');
    }
  } catch (error) {
    console.error('Error fetching post details:', error);
  }
};

onMounted(() => {
  if (route.params.id) {
    getPostDetails(route.params.id);
  }
});

watch(() => route.params.id, (newId) => {
  if (newId) {
    getPostDetails(newId);
  }
});
</script>

<style scoped>
.post-details {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
</style>
