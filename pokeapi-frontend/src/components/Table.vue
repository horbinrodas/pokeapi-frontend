<template>
  <h2>Pokémon</h2>
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>Nombre</th>
          <th>Altura</th>
          <th>Peso</th>
          <th>Experiencia Base</th>
          <th>Sprite</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="pokemon in pokemons" :key="pokemon.id" @click="modalRef.openModal(pokemon)" style="cursor: pointer;">
          <td>{{ pokemon.id }}</td>
          <td>{{ pokemon.name }}</td>
          <td>{{ pokemon.height }}</td>
          <td>{{ pokemon.weight }}</td>
          <td>{{ pokemon.base_experience }}</td>
          <td><img :src="pokemon.sprites.front_default"/></td>
        </tr>
      </tbody>
    </table>

    <div class="pagination">
      <button @click="prevPage" :disabled="currentPage === 1"><</button>
      <span>Pagina {{ currentPage }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">></button>
    </div>
    <Modal ref="modalRef" />
</template>

<script setup>
  import { ref, onMounted, watch } from 'vue'
  import Modal from './Modal.vue'
  const modalRef = ref(null)

  const pokemons = ref([])
  const currentPage = ref(1)
  const limit = 20
  const totalPages = ref(50)

  const fetchPokemonData = async () => {
    const offset = (currentPage.value - 1) * limit
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit}&offset=${offset}`)
    const data = await response.json()

    const detailedData = await Promise.all(
      data.results.map(async (pokemon) => {
        const res = await fetch(pokemon.url)
        return await res.json()
      })
    )
    pokemons.value = detailedData    
  }

  const nextPage = () => {
    if (currentPage.value < totalPages.value) {
      currentPage.value++
    }
  }

  const prevPage = () => {
    if (currentPage.value > 1) {
      currentPage.value--
    }
  }

  watch(currentPage, fetchPokemonData)
  onMounted(fetchPokemonData)
</script>

<style scoped>
  table {
    border-collapse: collapse;
    width: 100%;
  }
  th, td {
    border: 1px solid #ccc;
    padding: 8px;
  }
  .pagination {
    margin-top: 1rem;
    display: flex;
    gap: 1rem;
    align-items: center;
  }
</style>

