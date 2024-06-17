<template>
  <div>
    <form @submit.prevent="handleSubmit">
      <div class="mb-3">
        <label for="title" class="form-label">Назва категорії</label>
        <input type="text" class="form-control" id="title" v-model="formData.title" required>
      </div>
      <div class="mb-3">
        <label for="description" class="form-label">Опис категорії</label>
        <textarea class="form-control" id="description" v-model="formData.description"></textarea>
      </div>
      <button type="submit" class="btn btn-primary">Створити категорію</button>
    </form>

    <div v-if="message" class="mt-3 alert alert-{{ message.type }}" role="alert">
      {{ message.text }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const formData = ref({
  title: '',
  description: ''
});

const message = ref<{ text: string; type: 'success' | 'danger' } | null>(null);

const router = useRouter();

const handleSubmit = async () => {
  try {
    const response = await fetch('http://127.0.0.1:8000/api/blog/categories', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(formData.value)
    });

    if (response.ok) {
      message.value = { text: 'Категорія успішно створена!', type: 'success' };
      // Опціонально: можна перенаправити користувача на сторінку з деталями новоствореної категорії
      await router.push(`/BlogCategory/${response.id}`);
    } else {
      throw new Error('Сталася помилка при створенні категорії');
    }
  } catch (error) {
    console.error('Помилка при створенні категорії:', error);
    message.value = { text: 'Помилка при створенні категорії', type: 'danger' };
  }
};
</script>

<style scoped>
.form-control {
  width: 100%;
  max-width: 400px;
}
</style>
