<template>
  <div class="container">
    <div class="filters">
      <input v-model="filters.name" type="text" placeholder="Name" />
      <select v-model="filters.status">
        <option value="">All Status</option>
        <option value="alive">Alive</option>
        <option value="dead">Dead</option>
        <option value="unknown">Unknown</option>
      </select>
      <button @click="applyFilters">Apply</button>
    </div>
    <div class="cards">
      <div class="card" v-for="character in characters" :key="character.id">
        <img :src="character.image" :alt="character.name" />
        <h3>{{ character.name }}</h3>
        <p>Status: {{ character.status }}</p>
        <p>Species: {{ character.species }}</p>
        <p>Gender: {{ character.gender }}</p>
      </div>
    </div>
    <div class="pagination">
      <button @click="previousPage" :disabled="page === 1">Previous</button>
      <span>Page {{ page }}</span>
      <button @click="nextPage" :disabled="!info.next">Next</button>
    </div>
    <div>Made With love By Ehteshamul Haque</div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const characters = ref([]);
    const page = ref(1);
    const info = ref({});
    const filters = ref({
      name: '',
      status: ''
    });

    const fetchCharacters = async () => {
      const params = {
        page: page.value,
        name: filters.value.name,
        status: filters.value.status
      };
      try {
        const response = await axios.get('https://rickandmortyapi.com/api/character', { params });
        characters.value = response.data.results;
        info.value = response.data.info;
      } catch (error) {
        console.error(error);
      }
    };

    const applyFilters = () => {
      page.value = 1;
      fetchCharacters();
    };

    const nextPage = () => {
      if (info.value.next) {
        page.value++;
        fetchCharacters();
      }
    };

    const previousPage = () => {
      if (page.value > 1) {
        page.value--;
        fetchCharacters();
      }
    };

    onMounted(fetchCharacters);

    return {
      characters,
      page,
      info,
      filters,
      applyFilters,
      nextPage,
      previousPage
    };
  }
};
</script>

<style>
.container {
  padding: 20px;
}

.filters {
  margin-bottom: 20px;
}

.cards {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.card {
  border: 1px solid #ccc;
  padding: 10px;
  width: 200px;
  text-align: center;
}

.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: center;
  gap: 10px;
}
</style>
