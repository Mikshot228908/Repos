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

    onMounted(async () => {//Данная функция  вызывается после того, как компонент Vue был добавлен на страницу
      fetchCharacters();
    });

    const fetchCharacters = async () => {//Здесь мы получаем данные с API и при успешной операции записываем  в формат JSON

      try {
        const response = await fetch('https://rickandmortyapi.com/api/character');
        const data = await response.json();
        characters.value = data.results;// Переменная character, отвечает за отображение информации о персонажах с API
      } catch (error) {
        console.error("Ошибка при получении данных с API", error);
      }
    };

    const totalPages = computed(() => Math.ceil(characters.value.length / itemsPerPage));
    //Определяет общие число страниц в  пагинации
    const paginatedCharacters = computed(() => {//Фильтрация персонажей по имени и статусу 
      const start = (currentPage.value - 1) * itemsPerPage;
      const end = currentPage.value * itemsPerPage;
      return characters.value
        .filter(ch => ch.name.toLowerCase().includes(nameFilter.value.toLowerCase()))
        .filter(ch => ch.status.toLowerCase().includes(statusFilter.value.toLowerCase()))
        .slice(start, end);
    });

    const nextPage = () => {//Функция для перехода на следующую страницу
      if (currentPage.value < totalPages.value) {
        currentPage.value++;
      }
    };

    const previousPage = () => {//Функция для перехода на предыдущую страницу
      if (currentPage.value > 1) {
        currentPage.value--;
      }
    };

    const applyFilters = () => {
      currentPage.value = 1; // Возвращает на первую страницу при применении фильтров
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
