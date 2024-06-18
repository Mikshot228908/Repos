<template>
  <div>
    <input v-model="nameFilter" placeholder="Фильтр по имени">
    <input v-model="statusFilter" placeholder="Фильтр по статусу">
    <button @click="applyFilters">Применить</button>
    <button @click="previousPage" :disabled="currentPage === 1">Предыдущая страница</button>
    <button @click="nextPage" :disabled="currentPage === totalPages">Следующая страница</button>
    <div v-for="ch in paginatedCharacters" :key="ch.id">
      <img :src="ch.image" alt="Character Image" />
      <h4><p>{{ ch.name }}-{{ ch.status }}</p></h4>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue';

export default {
  setup() {
    const characters = ref([]);
    const currentPage = ref(1);
    const itemsPerPage = 10;
    const nameFilter = ref('');
    const statusFilter = ref('');

    onMounted(async () => {
      fetchCharacters();
    });

    const fetchCharacters = async () => {
      try {
        const response = await fetch('https://rickandmortyapi.com/api/character');
        const data = await response.json();
        characters.value = data.results;
      } catch (error) {
        console.error("Ошибка при получении данных от API", error);
      }
    };

    const totalPages = computed(() => Math.ceil(characters.value.length / itemsPerPage));

    const paginatedCharacters = computed(() => {
      const start = (currentPage.value - 1) * itemsPerPage;
      const end = currentPage.value * itemsPerPage;
      return characters.value
        .filter(ch => ch.name.toLowerCase().includes(nameFilter.value.toLowerCase()))
        .filter(ch => ch.status.toLowerCase().includes(statusFilter.value.toLowerCase()))
        .slice(start, end);
    });

    const nextPage = () => {
      if (currentPage.value < totalPages.value) {
        currentPage.value++;
      }
    };

    const previousPage = () => {
      if (currentPage.value > 1) {
        currentPage.value--;
      }
    };

    const applyFilters = () => {
      currentPage.value = 1; // Сбросить на первую страницу при применении фильтров
    };

    return {
      characters,
      paginatedCharacters,
      nextPage,
      previousPage,
      currentPage,
      totalPages,
      nameFilter,
      statusFilter,
      applyFilters
    };
  }
};
</script>


<style scoped>
p {
  color: blueviolet;
  font-size: larger;
  font-style: oblique 90deg;
}
</style>