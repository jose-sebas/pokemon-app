<script lang="ts" setup>
import type { Pokemon } from '@/types'

defineProps<{
  pokemon: Pokemon
}>()

const emit = defineEmits<{
  favorite: [id: number]
  unfavorite: [id: number]
}>()
</script>

<template>
  <div class="pokemon-card">
    <div style="display: flex; justify-content: space-between; align-items: center; width: 100%">
      <h3>{{ pokemon.name }}</h3>
      <div v-if="pokemon.isFavorite" @click="emit('unfavorite', pokemon.id)" class="favorite">
        Is favorite
      </div>
      <div v-else @click="(emit('favorite', pokemon.id), pokemon.id)" class="not-favorite">
        Not favorite
      </div>
    </div>
    <div>
      <img :src="`${pokemon.sprites}`" alt="" />
    </div>
    <div style="display: flex; gap: 8px; align-items: center">
      Types: <span v-for="type in pokemon.types" class="pokemon-type">{{ type }}</span>
    </div>
  </div>
</template>

<style scoped>
.pokemon-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-width: 300px;
  min-height: 200px;
  border-radius: 12px;
  border: solid;
  border-color: gray;
  border-width: 1px;
  padding: 12px;
}
.pokemon-type {
  border: solid;
  padding: 4px;
  border-radius: 8px;
  border-width: 1px;
  border-color: gray;
}
.favorite {
  border: solid;
  border-color: gold;
  color: gold;
  padding: 4px;
  border-width: 1px;
  border-radius: 8px;
}
.not-favorite {
  cursor: pointer;
  border: solid;
  border-color: gray;
  color: gray;
  padding: 4px;
  border-width: 1px;
  border-radius: 8px;
}
</style>
