<script setup lang="ts">
import { ref, computed } from 'vue';
import { $fetch } from 'ohmyfetch'; // Підключіть бібліотеку для запитів HTTP

const columns = [
  { key: 'id', label: '#' },
  { key: 'title', label: 'Назва' },
  { key: 'description', label: 'Опис' },
  { key: 'created_at', label: 'Дата створення' },
];

const page = ref(1);
const page_count = 5;

interface Category {
  id: number;
  title: string;
  description: string;
  created_at: string;
}

const categories = ref<Category[]>([]);

const getCategories = async () => {
  try {
    const response = await $fetch<Category[]>('http://127.0.0.1:8000/api/blog/categories');
    categories.value = response.map(category => ({
      ...category,
      created_at: formatDate(category.created_at), // Форматуємо дату при отриманні з сервера
    }));
  } catch (error) {
    console.error('Error fetching categories:', error);
  }
};
getCategories();

const formatDate = (dateString: string): string => {
  const date = new Date(dateString);
  return date.toLocaleDateString('uk-UA'); // Форматуємо дату у відповідний формат, наприклад, український
};

const rows = computed(() => {
  return categories.value.map((category) => ({
    id: category.id,
    title: category.title,
    description: category.description,
    created_at: category.created_at,
  }));
});

const total = computed(() => rows.value.length);
const paginated_rows = computed(() => {
  return rows.value.slice((page.value - 1) * page_count, page.value * page_count);
});
</script>

<template>
  <UTable :columns="columns" :rows="paginated_rows" :total="rows.length" />
  <div>
    <UPagination v-model="page" :page-count="page_count" :total="total" />
  </div>
</template>
