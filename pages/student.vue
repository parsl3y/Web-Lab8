<script setup>
const columns = [
  {
    key: 'title',
    label: 'Title'
  },
  {
    key: 'description',
    label: 'Description'
  },
  {
    key: 'price',
    label: 'Price'
  },
  {
    key: 'rating',
    label: 'Rating'
  },
  {
    key: 'brand',
    label: 'Brand'
  },
  {
    key: 'category',
    label: 'Category'
  },
  {
    key: 'thumbnail',
    label: 'Thumbnail'
  }
]

const { data } = await useFetch('https://dummyjson.com/products');
const people = data.value.products;

const q = ref('');
const page = ref(1);
const pageCount = 5;


const filtredData =() => {
  if (!q.value) {
    return people;
  }

  return people.filter((person) => {
    return Object.values(person).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase());
    });
  });
};

const filtredRows =  computed(() => {
  return filtredData();
})
const rows = computed(() => {
  if (filtredData().length <= 5 ) {
    page.value = 1;
  }
  const slice = filtredData()
      .slice((page.value - 1) * pageCount, (page.value) * pageCount);
  console.log(slice)
  return slice });
</script>
<template>
  <title>Student list</title>

  <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
    <UInput v-model="q" placeholder="Filter people..." />
  </div>

  <div>
    <UTable :rows="rows" :columns="columns">
      <template #thumbnail-data="{row}">
        <img :src="row.thumbnail" alt="" class="w-[100px] h-[100px]">
      </template>
      <template #description-data="{row}">
        <div class="text-wrap">{{ row.description }}</div>
      </template>
      <template #price-data="{row}">
        <div>{{ row.price }}</div>
      </template>
      <template #rating-data="{row}">
        <div :class="{ 'bg-red-500/40': row.rating < 4.5, 'bg-green-500/40': row.rating >= 4.5 }" class="px-3 py-2">
          {{ row.rating }}
        </div>
      </template>
      <template #brand-data="{row}">
        <div>{{ row.brand }}</div>
      </template>
      <template #category-data="{row}">
        <div>{{ row.category }}</div>
      </template>
    </UTable>
    <div class="flex justify-center px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="page" :page-count="pageCount" :total="filtredRows.length" />
    </div>
  </div>
</template>


