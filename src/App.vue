<template>
  <div id="app">
    <h1>Rick and Morty Characters</h1>
    
    <div>
      <input v-model="filters.name" placeholder="Filter by name" />
      <select v-model="filters.status">
        <option value="">All</option>
        <option value="alive">Alive</option>
        <option value="dead">Dead</option>
        <option value="unknown">Unknown</option>
      </select>
      <button @click="applyFilters">Apply</button>
    </div>

    <div class="character-list">
      <CharacterCard v-for="character in characters" :key="character.id" :character="character" />
    </div>

    <div class="pagination">
      <button @click="prevPage" :disabled="page === 1">Previous</button>
      <span>Page {{ page }}</span>
      <button @click="nextPage" :disabled="!info.next">Next</button>
    </div>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from 'vue';
import CharacterCard from './components/CharacterCard.vue';

export default {
  name: 'App',
  components: {
    CharacterCard
  },
  setup() {
    const characters = ref([]);
    const page = ref(1);
    const filters = reactive({
      name: '',
      status: ''
    });
    const info = reactive({
      next: null,
      prev: null
    });

    const fetchCharacters = async () => {
      const params = new URLSearchParams();
      params.append('page', page.value);
      if (filters.name) params.append('name', filters.name);
      if (filters.status) params.append('status', filters.status);
      
      const response = await fetch(`https://rickandmortyapi.com/api/character/?${params.toString()}`);
      const data = await response.json();
      characters.value = data.results;
      info.next = data.info.next;
      info.prev = data.info.prev;
    };

    const applyFilters = () => {
      page.value = 1;
      fetchCharacters();
    };

    const nextPage = () => {
      if (info.next) {
        page.value++;
        fetchCharacters();
      }
    };

    const prevPage = () => {
      if (info.prev) {
        page.value--;
        fetchCharacters();
      }
    };

    onMounted(fetchCharacters);

    return {
      characters,
      page,
      filters,
      info,
      applyFilters,
      nextPage,
      prevPage
    };
  }
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  padding: 16px;
}
.character-list {
  display: flex;
  flex-wrap: wrap;
}
.pagination {
  display: flex;
  justify-content: center;
  margin-top: 16px;
}
</style>
