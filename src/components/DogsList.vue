<template>
  <table class="dogs-table">
    <thead>
      <tr>
        <th>ID</th><th>Name</th><th>Breed</th><th>Birth Date</th><th>Human Age</th>
        <th>Dog Age</th><th>Gender</th><th>Owner</th><th>Phone</th><th>Actions</th>
      </tr>
    </thead>
    <tbody>
      <tr v-if="items.length === 0">
        <td colspan="10">No dogs found.</td>
      </tr>
      <tr v-for="dog in items" :key="dog._id">
        <td>{{ dog.idDog }}</td>
        <td>{{ dog.name }}</td>
        <td>{{ dog.breed }}</td>
        <td>{{ dog.birth_date.split('T')[0] }}</td>
        <td>{{ dog.human_age }}</td>
        <td>{{ dog.dog_age }}</td>
        <td>{{ dog.gender }}</td>
        <td>{{ dog.owner }}</td>
        <td>{{ dog.phone }}</td>
        <td>
          <button @click="$emit('edit', dog)">Edit</button>
          <button @click="$emit('delete', dog)">Delete</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script setup lang="ts">
import '../assets/styles/DogsList.css'

import type { Dog } from '../interfaces/Dog'
const props = defineProps<{ items: Dog[] }>()
</script>
