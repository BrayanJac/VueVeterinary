<template>
  <div class="modal-backdrop" @click="close">
    <div class="modal-content" @click.stop>
      <h3>{{ mode === 'create' ? 'Add Dog' : 'Edit Dog' }}</h3>
      <form @submit.prevent="onSubmit">
        <label>IdDog: <input name="idDog" v-model="form.idDog" required /></label>
        <label>Name: <input name="name" v-model="form.name" required /></label>
        <label>Breed: <input name="breed" v-model="form.breed" required /></label>
        <label>Birth Date: <input type="date" name="birth_date" v-model="form.birth_date" required /></label>
        <label>Gender:
          <select name="gender" v-model="form.gender" required>
            <option value="">--Select--</option>
            <option>Male</option><option>Female</option>
          </select>
        </label>
        <label>Owner: <input name="owner" v-model="form.owner" required /></label>
        <label>Phone: <input name="phone" v-model="form.phone" required /></label>
        <button type="submit">{{ mode === 'create' ? 'Add' : 'Save' }}</button>
        <button type="button" @click="close">Cancel</button>
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import '../assets/styles/DogFormModal.css'

import type { Dog } from '../interfaces/Dog'
import { defineProps, defineEmits, reactive } from 'vue'

const props = defineProps<{ mode: 'create' | 'edit'; dog?: Dog }>()
const emit = defineEmits<{
  (e: 'submit', form: Dog): void
  (e: 'close'): void
}>()

const form = reactive<Dog>(props.dog
  ? { ...props.dog, birth_date: props.dog.birth_date.split('T')[0] }
  : { idDog: '', name: '', breed: '', birth_date: '', gender: '', owner: '', phone: '' }
)

const onSubmit = () => emit('submit', { ...form })
const close = () => emit('close')
</script>

