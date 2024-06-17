<template>
  <div v-if="category" class="category-details">
    <h3>Деталі категорії</h3>
    <p><strong>ID:</strong> {{ category.id }}</p>
    <p><strong>Назва:</strong> {{ category.title }}</p>
    <p><strong>Опис:</strong> {{ category.description }}</p>
    <p><strong>Дата створення:</strong> {{ formattedCreatedAt }}</p>
  </div>
  <div v-else>
    <p>Loading...</p>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from 'vue';
import { useRoute } from 'vue-router';

const category = ref(null);
const route = useRoute();

const getCategoryDetails = async (id) => {
  try {
    const response = await fetch(`http://127.0.0.1:8000/api/blog/categories/${id}`);
    if (response.ok) {
      const data = await response.json();
      category.value = data;
    } else {
      throw new Error('Failed to fetch category details');
    }
  } catch (error) {
    console.error('Error fetching category details:', error);
  }
};

onMounted(() => {
  if (route.params.id) {
    getCategoryDetails(route.params.id);
  }
});

watch(() => route.params.id, (newId) => {
  if (newId) {
    getCategoryDetails(newId);
  }
});

// Функція для форматування дати
const formattedCreatedAt = computed(() => {
  if (category.value && category.value.created_at) {
    const date = new Date(category.value.created_at);
    return `${date.toLocaleDateString()} ${date.toLocaleTimeString()}`;
  }
  return '-';
});
</script>

<style scoped>
.category-details {
  margin-top: 20px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
</style>
