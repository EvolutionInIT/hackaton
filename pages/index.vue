<script setup>
const { data } = await $fetch("http://localhost/api/summary/collections");
</script>
<template>
  <div class="flex justify-center mb-4">
    <NuxtLink
      :to="{ name: 'collection-new2' }"
      class="px-6 py-2 text-white bg-blue-500 rounded hover:bg-blue-600 transition duration-300"
    >
      Создать новую коллекцию
    </NuxtLink>

    <NuxtLink
      :to="{ name: 'upload' }"
      class="px-6 py-2 text-white bg-blue-500 rounded hover:bg-blue-600 transition duration-300 ml-10"
    >
      Загрузить новые резюме
    </NuxtLink>
  </div>

  <div class="flex flex-wrap">
    <NuxtLink
      v-for="collection in data"
      :key="collection.id"
      :to="{ name: 'collection-id', params: { id: collection.id } }"
      class="rounded-lg shadow-lg p-4 m-2 bg-white hover:bg-gray-100 transition duration-300 no-underline"
    >
      <h3 class="text-lg font-semibold text-gray-800">
        {{ collection.title }}
      </h3>
      <p class="text-gray-600">id: {{ collection.id }}</p>
    </NuxtLink>
  </div>
</template>
