<template>
  <div class="container">
    <h1>Dogs CRUD - Veterinary</h1>

    <SearchBar
      :query="searchQuery"
      @update:query="val => searchQuery = val"
      @search="onSearch"
      @clear="onClear"
      @create="onCreate"
    />

    <DogsList
      :items="isSearching ? filteredDogs : dogs"
      @edit="openEditModal"
      @delete="openDeleteModal"
    />

    <DogFormModal
      v-if="showForm"
      :mode="modalMode"
      :dog="currentDog"
      @submit="onSubmitForm"
      @close="() => showForm = false"
    />

    <ConfirmDeleteModal
      v-if="showDelete"
      :dog="dogToDelete"
      @confirm="onConfirmDelete"
      @close="() => showDelete = false"
    />
  </div>
</template>

<script setup lang="ts">
import './assets/styles/App.css'

import { ref, onMounted, computed } from 'vue'
import type { Dog } from './interfaces/Dog'
import SearchBar from './components/SearchBar.vue'
import DogsList from './components/DogsList.vue'
import DogFormModal from './components/DogFormModal.vue'
import ConfirmDeleteModal from './components/ConfirmDeleteModal.vue'

const API_BASE = 'https://apiveterinaria-eche.onrender.com/veterinary'

const dogs = ref<Dog[]>([])
const filteredDogs = ref<Dog[]>([])
const searchQuery = ref('')
const isSearching = ref(false)

const showForm = ref(false)
const modalMode = ref<'create' | 'edit'>('create')
const currentDog = ref<Dog | undefined>(undefined)

const showDelete = ref(false)
const dogToDelete = ref<Dog | undefined>(undefined)

function calculateHumanAge(bd: string): number {
  const birthDate = new Date(bd)
  const now = new Date()
  let age = now.getFullYear() - birthDate.getFullYear()
  if (now.getMonth() < birthDate.getMonth() || (now.getMonth() === birthDate.getMonth() && now.getDate() < birthDate.getDate())) age--
  return age
}

function calculateDogAge(humanAge: number): number {
  if (humanAge === 1) return 15
  if (humanAge === 2) return 24
  if (humanAge >= 3) return 24 + (humanAge - 2) * 4
  return 0
}

async function fetchDogs() {
  const res = await fetch(`${API_BASE}/dogs`)
  const data: Dog[] = await res.json()
  dogs.value = data.map(d => ({
    ...d,
    human_age: calculateHumanAge(d.birth_date),
    dog_age: calculateDogAge(calculateHumanAge(d.birth_date))
  }))
}

function onSearch() {
  if (!searchQuery.value.trim()) {
    isSearching.value = false
    filteredDogs.value = []
    return
  }
  filteredDogs.value = dogs.value.filter(d =>
    d.idDog === searchQuery.value.trim() ||
    d.name === searchQuery.value.trim() ||
    d.owner === searchQuery.value.trim()
  )
  isSearching.value = true
}

function onClear() {
  searchQuery.value = ''
  isSearching.value = false
  filteredDogs.value = []
}

function onCreate() {
  modalMode.value = 'create'
  currentDog.value = undefined
  showForm.value = true
}

function openEditModal(dog: Dog) {
  modalMode.value = 'edit'
  currentDog.value = dog
  showForm.value = true
}

function openDeleteModal(dog: Dog) {
  dogToDelete.value = dog
  showDelete.value = true
}

async function onSubmitForm(form: Dog) {
  const method = modalMode.value === 'create' ? 'POST' : 'PUT'
  const url = modalMode.value === 'create'
    ? `${API_BASE}/dog`
    : `${API_BASE}/dog/${currentDog.value!._id}`

  await fetch(url, {
    method,
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(form)
  })
  await fetchDogs()
  showForm.value = false
}

async function onConfirmDelete() {
  if (!dogToDelete.value) return
  await fetch(`${API_BASE}/dog/${dogToDelete.value._id}`, { method: 'DELETE' })
  await fetchDogs()
  showDelete.value = false
}

onMounted(fetchDogs)
</script>

<style scoped>
.container { max-width: 800px; margin: 2rem auto; font-family: Arial, sans-serif; }
h1 { text-align: center; margin-bottom: 1.5rem; }
</style>
