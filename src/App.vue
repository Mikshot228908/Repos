<template>
  <div>
    <button @click="previousPage" :disabled="currentPage === 1">Previous Page</button>
    <button @click="nextPage" :disabled="currentPage === totalPages">Next Page</button>
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
      return characters.value.slice(start, end);
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

    return {
      characters,
      paginatedCharacters,
      nextPage,
      previousPage,
      currentPage,
      totalPages
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
