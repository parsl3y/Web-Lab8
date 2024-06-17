<script setup >


const columns = [
  {
    key: 'title',
    label: 'Title',
    sortable: true
  },
  {
    key: 'description',
    label: 'Description',
    sortable: true
  },
  {
    key: 'price',
    label: 'Price',
    sortable: true
  },
  {
    key: 'rating',
    label: 'Rating',
    sortable: true
  },
  {
    key: 'brand',
    label: 'Brand',
    sortable: true
  },
  {
    key: 'category',
    label: 'Category',
    sortable: true
  },
  {
    key: 'thumbnail',
    label: 'Thumbnail',
    sortable: false
  }
]

const { data } = await useFetch('https://dummyjson.com/products');
const people = data.value.products;

const q = ref('');
const page = ref(1);
const pageCount = 4;

const filteredData = computed(() => {
  if (!q.value) {
    return people;
  }

  return people.filter(person => Object.values(person).some(value => String(value).toLowerCase().includes(q.value.toLowerCase())));
});

const sortColumn = ref(null);
const sortDirection = ref('asc');

const sortedData = computed(() => {
  if (!sortColumn.value) {
    return filteredData.value;
  }

  return [...filteredData.value].sort((a, b) => {
    const column = sortColumn.value;

    const aValue = a[column];
    const bValue = b[column];

    if (aValue === bValue) {
      return 0;
    }

    if (sortDirection.value === 'asc') {
      return aValue < bValue ? -1 : 1;
    } else {
      return aValue > bValue ? -1 : 1;
    }
  });
});

const rows = computed(() => {
  if (sortedData.value.length <= 5) {
    page.value = 1;
  }
  const slice = sortedData.value.slice((page.value - 1) * pageCount, page.value * pageCount);
  return slice;
});
// Фільтр за алфавітом і навпаки
const handleSort = (columnKey) => {
  if (sortColumn.value === columnKey) {
    sortDirection.value = sortDirection.value === 'asc' ? 'desc' : 'asc';
  } else {
    sortColumn.value = columnKey;
    sortDirection.value = 'asc';
  }
};
</script>

<template>

  <title>Student list</title>


  <div class="flex px-3 py-3.5 border-b border-gray-200 dark:border-gray-700">
    <UInput v-model="q" placeholder="Filter people..."/><router-link to="./graphic" class="text-blue-500 hover:underline"> <pre>  Перейти до графіків   </pre></router-link>
    <router-link to="./BlogPosts" class="text-blue-500 hover:underline"> <pre> Перейти до Постів </pre> </router-link>
    <router-link to="./BlogCategories" class="text-blue-500 hover:underline"> <pre> Перейти до Категорій </pre></router-link>
  </div>



  <div>
    <UTable :columns="columns" :rows="rows" >
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
      <template #title-header="{column}">
        <div @click="handleSort(column.key)" style="cursor: pointer;">{{ column.label }}</div>
      </template>
      <template #description-header="{column}">
        <div @click="handleSort(column.key)" style="cursor: pointer;">{{ column.label }}</div>
      </template>
      <template #price-header="{column}">
        <div @click="handleSort(column.key)" style="cursor: pointer;">{{ column.label }}</div>
      </template>
      <template #rating-header="{column}">
        <div @click="handleSort(column.key)" style="cursor: pointer;">{{ column.label }}</div>
      </template>
      <template #brand-header="{column}">
        <div @click="handleSort(column.key)" style="cursor: pointer;">{{ column.label }}</div>
      </template>
      <template #category-header="{column}">
        <div @click="handleSort(column.key)" style="cursor: pointer;">{{ column.label }}</div>
      </template>
      <template #thumbnail-header="{column}">

      </template>
    </UTable>

    <div class="flex justify-center px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="page" :page-count="pageCount" :total="filteredData.length"/>
    </div>

  </div>
</template>
