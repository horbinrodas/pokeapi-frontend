<template>
  <div v-if="showModal" class="modal-overlay" @click.self="closeModal">
    <div class="modal-content">
      <button @click="closeModal">X</button>
      <h3>{{ selectedPokemon.name }}</h3>
      <div class="slider">
        <swiper :slides-per-view="1" navigation pagination>
          <swiper-slide v-for="(img, index) in spriteImages" :key="index">
            <img :src="img" :alt="`Sprite ${index}`" />
          </swiper-slide>
        </swiper>
      </div>
      <ul>
        <li>Altura: {{ selectedPokemon.height }}</li>
        <li>Peso: {{ selectedPokemon.weight }}</li>
        <li>Experiencia Base: {{ selectedPokemon.base_experience }}</li>
        <li v-for="stat in selectedPokemon.stats" :key="stat.stat.name">
          {{ stat.stat.name }}: {{ stat.base_stat }}
        </li>
      </ul>
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
    spriteImages.value = Object.values(pokemon.sprites).filter(
      (img) => typeof img === 'string' && img.includes('http')
    )
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
    background: blue;
    padding: 1rem;
    border-radius: 8px;
    width: 80%;
    max-width: 500px;
  }
  .slider img {
    width: 100%;
    height: auto;
  }
  button {
    position: absolute;
    top: 10px; right: 10px;
    background: transparent;
    border: none;
    font-size: 1.5rem;
    cursor: pointer;
  }
</style>
