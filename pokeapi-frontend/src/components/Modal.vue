<template>
  <div v-if="showModal" class="modal-overlay" @click.self="closeModal">
    <div class="modal-content">
      <img class="bg-pokedex" src="/pokedex.png" alt="Pokedex" />
      <button @click="closeModal">X</button>
      <h3>{{ selectedPokemon.name }}</h3>
      <div class="slider">
        <swiper :slides-per-view="1" navigation pagination>
          <swiper-slide v-for="(img, index) in spriteImages" :key="index">
            <img :src="img" :alt="`Sprite ${index}`" />
          </swiper-slide>
          <swiper-slide>
            <ul>
              <li>Altura: {{ selectedPokemon.height }}</li>
              <li>Peso: {{ selectedPokemon.weight }}</li>
              <li>Experiencia Base: {{ selectedPokemon.base_experience }}</li>
              <li>Abilidades: {{ selectedPokemon.abilities.map(ability => ability.ability.name).join(', ') }}</li>
              <li>Tipos: {{ selectedPokemon.types.map(type => type.type.name).join(', ') }}</li>
              <li>Especie: {{ selectedPokemon.species.name }}</li>
              <li v-for="stat in selectedPokemon.stats" :key="stat.stat.name">
                {{ stat.stat.name }}: {{ stat.base_stat }}
              </li>
            </ul>
            <audio controls>
              <source :src="selectedPokemon.cries.latest" type="audio/mpeg">
            </audio>
          </swiper-slide>
          <swiper-slide>
            <div class="table-container">
              <table>
                <thead>
                  <tr>
                    <th>#</th>
                    <th>Movimientos</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(m, i) in selectedPokemon.moves" :key="m.move.name + i">
                    <td>{{ i + 1 }}</td>
                    <td>{{ m.move.name }}</td>
                  </tr>
                  <tr v-if="!selectedPokemon.moves || !selectedPokemon.moves.length">
                    <td>No hay movimientos</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </swiper-slide>
        </swiper>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { ref } from 'vue'
  import { Swiper, SwiperSlide } from 'swiper/vue'
  import 'swiper/css'
  import 'swiper/css/navigation'
  import 'swiper/css/pagination'

  const showModal = ref(false)
  const selectedPokemon = ref({})
  const spriteImages = ref([])

  const openModal = (pokemon) => {
    selectedPokemon.value = pokemon
    const sprites = pokemon.sprites || {}
    spriteImages.value = []
    const official = sprites.other?.['official-artwork']?.front_default
    if (official) spriteImages.value.push(official)
    if (sprites.front_default) spriteImages.value.push(sprites.front_default)
    if (sprites.back_default) spriteImages.value.push(sprites.back_default)

    showModal.value = true
  }

  const closeModal = () => {
    showModal.value = false
  }

  defineExpose({ openModal })
</script>

<style scoped>
  .modal-overlay {
    position: fixed;
    top: 0; left: 0;
    width: 100%; height: 100%;
    background: rgba(0, 0, 0, 0.6);
    display: flex; justify-content: center; align-items: center;
  }
  .modal-content {
    position: relative;
    background: #FF0000;
    opacity: 0.75;
    border-radius: 8px;
    width: 80%;
    height: 100%;
    max-width: 40%;
    color: white;
  }
  @media screen and (max-width: 768px) {
    .modal-content {
      width: 90%;
      max-width: 90%;
    }
  }
  .slider img {
    width: 100%;
    height: auto;
  }
  .bg-pokedex {
    position: absolute;
    width: 100%;
    height: 100%;
    opacity: 0.75;
    z-index: -1;
  }
  button {
    position: absolute;
    top: 10px; right: 10px;
    background: transparent;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
  }
  audio {
    width: 80%;
    padding-left: 3rem;
  }
  ul {
    list-style-type: disc;
    padding-left: 6rem;
    padding-top: 6rem;
    font-size: x-large;
  }
  h3 {
    text-align: center;
    margin-top: 1rem;
  }
  .table-container {
    max-height: 240px; 
    overflow-y: auto; 
    padding: 6rem;
    top: 5rem;
    scrollbar-color: transparent transparent;
  }
  table{
    width: 100%; 
    border-collapse: collapse; 
    color: white;
  }
  th, td {
    border: 1px solid #ccc;
    padding: 8px;
    font-size: x-large;
  }
</style>
