
<template>
  <div class="w-1/3 m-auto">
    <div class="p-5">
      <nuxt-link class="mt-20 bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded" to="/categories">Назад</nuxt-link>
    </div>
    <div class="p-5 border-2 rounded">
      <h1 class="text-center mb-2">Редагувати категорію</h1>
      <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
        <UFormGroup label="Назва" name="title">
          <UInput v-model="state.title" />
        </UFormGroup>

        <UFormGroup label="Slug" name="slug">
          <UInput v-model="state.slug" />
        </UFormGroup>

        <UFormGroup label="Опис" name="description">
          <UInput v-model="state.description" />
        </UFormGroup>

        <UFormGroup label="Батьківська категорія" name="parent_id">
          <USelect v-model="state.parent_id" :options="categoryOptions" />
        </UFormGroup>

        <UButton class="flex m-auto mt-5" type="submit">
          Зберегти
        </UButton>
      </UForm>
    </div>
  </div>
</template>

<script setup lang="ts">
import { z } from 'zod';
import { reactive, ref, computed, onMounted } from 'vue';
import axios from 'axios';
import { useRouter, useRoute } from 'vue-router';

interface Category {
  id: number;
  title: string;
  slug: string;
  description: string;
  created_at: string;
  updated_at: string;
  deleted_at: string;
  parent_id: number;
  parent_category: Category;
}

const categories = ref<Category[]>([]);
const route = useRoute();
const router = useRouter();

const getCategories = async () => {
  try {
    const response = await axios.get<Category[]>(`http://127.0.0.1:8000/api/blog/categories`);
    categories.value = response.data;
  } catch (error) {
    console.error('Помилка отримання категорій:', error);
  }
};

const getCategory = async (id: string) => {
  try {
    const response = await axios.get<Category>(`http://127.0.0.1:8000/api/blog/categories/${id}`);
    const category = response.data;
    state.title = category.title;
    state.slug = category.slug;
    state.description = category.description;
    state.parent_id = category.parent_id.toString();
  } catch (error) {
    console.error('Помилка отримання категорії:', error);
  }
};

onMounted(() => {
  getCategories();
  getCategory(route.params.id);
});

// Вибірка категорій для випадаючого списку
const categoryOptions = computed(() => {
  return categories.value.map(category => ({
    value: category.id.toString(), // Конвертація id в строку
    label: category.title
  }));
});

// Визначення схеми Zod для валідації
const schema = z.object({
  title: z.string().min(5, 'Назва повинна бути не менше 5 символів').max(200, 'Назва повинна бути не більше 200 символів'),
  slug: z.string().max(200, 'Slug повинен бути не більше 200 символів').optional(),
  description: z.string().min(3, 'Опис повинен бути не менше 3 символів').max(500, 'Опис повинен бути не більше 500 символів').optional(),
  parent_id: z.string().refine(value => {
    const numberValue = Number(value);
    return Number.isInteger(numberValue) && categories.value.some(category => category.id === numberValue);
  }, 'Батьківська категорія повинна бути валідним цілим числом і існувати серед категорій')
});

type Schema = z.infer<typeof schema>;

// Реактивний стан для даних форми
const state = reactive<Partial<Schema>>({
  title: undefined,
  slug: undefined,
  description: undefined,
  parent_id: undefined
});

// Функція обробки подання форми
async function onSubmit() {
  try {
    const result = schema.parse(state); // Валідація даних форми
    const dataToSend = {
      ...result,
      parent_id: Number(result.parent_id) // Конвертація parent_id в число перед відправкою
    };

    const id = route.params.id; // Отримання ID категорії з URL

    const csrfToken = csrfTokenElement.content; // Отримання CSRF токену з мета-тега

    const response = await axios.put(`http://127.0.0.1:8000/api/blog/categories/${id}`, dataToSend, {
      headers: {
        'X-CSRF-TOKEN': csrfToken // Додавання CSRF токену у заголовок запиту
      }
    });

    if (response.status === 200) {
      alert('Категорію успішно оновлено!');
      await router.push(`/categories`);
    } else {
      throw new Error('Не вдалося оновити категорію');
    }
  } catch (e) {
    if (e instanceof z.ZodError) {
      console.error(e.errors); // Обробка помилок валідації
    } else {
      console.error('Помилка при поданні форми:', e); // Обробка інших помилок
    }
  }
}
</script>

<style scoped>
.form-control {
  width: 100%;
  max-width: 400px;
}
</style>
