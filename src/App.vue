<script setup lang="ts">
import { ref, watch } from 'vue'
import debounce from 'lodash/debounce'
import PokemonCard from './components/PokemonCard.vue'
import type { Pokemon } from './types.ts'

const searchInput = ref<string>('')
const pokemons = ref<Pokemon[]>([])
const isLoading = ref(false)

const fetchPokemon = async (query: string) => {
  let endpoint = 'http://localhost:3000/api/pokemons'
  if (query) {
    endpoint = `http://localhost:3000/api/pokemon/${query.toLowerCase()}`
  }

  isLoading.value = true
  try {
    const response = await fetch(endpoint)
    if (response.ok) {
      const { results } = await response.json()
      pokemons.value = results
      console.log(pokemons.value)
    } else {
      pokemons.value = []
    }
  } catch (error) {
    console.error('Error connecting with the backend', error)
  } finally {
    isLoading.value = false
  }
}

const makeFavorite = async (id: number) => {
  try {
    const url = 'http://localhost:3000/api/favorites'

    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },

      body: JSON.stringify({
        pokemonId: id,
      }),
    })

    if (!response.ok) {
      throw new Error('it was not possible to save as favorite')
    }

    const data = await response.json()

    pokemons.value = pokemons?.value.map((pokemon) => {
      return pokemon.id === id ? { ...pokemon, isFavorite: true } : pokemon
    })
  } catch (error) {
    alert('there was an error when we try to mark as favorite')
  }
}

const debouncedSearch = debounce((newValue: string) => {
  fetchPokemon(newValue)
}, 500)

watch(
  searchInput,
  (newValue) => {
    debouncedSearch(newValue)
  },
  { immediate: true },
)
</script>

<template>
  <div style="display: flex; flex-direction: column; align-items: center">
    <h1>Pokedex</h1>
    <div><input type="text" v-model="searchInput" placeholder="Search a pokemon" /></div>
    <div v-if="searchInput">Searching for a pokemon "{{ searchInput }}"</div>
    <div class="container" v-if="!isLoading">
      <PokemonCard v-for="pokemon of pokemons" :pokemon="pokemon" @favorite="makeFavorite" />
    </div>
    <h2 v-if="pokemons.length == 0 && searchInput">There are not results of this search</h2>
  </div>
</template>

<style scoped>
.container {
  margin-top: 56px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: row;
  flex-wrap: wrap;
  gap: 48px;
}
</style>
