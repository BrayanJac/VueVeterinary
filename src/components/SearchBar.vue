<template>
  <div class="search-bar">
    <input
      v-model="localQuery"
      @input="$emit('update:query', localQuery)"
      placeholder="Search by ID, name or owner"
    />
    <button @click="$emit('search')">Search</button>
    <button @click="$emit('clear')">Clear</button>
    <button @click="$emit('create')">Add Dog</button>
  </div>
</template>

<script setup lang="ts">
import '../assets/styles/SearchBar.css'

import { ref, watch, defineProps } from 'vue'
const props = defineProps<{ query: string }>()
const localQuery = ref(props.query)
watch(() => props.query, q => (localQuery.value = q))
</script>
