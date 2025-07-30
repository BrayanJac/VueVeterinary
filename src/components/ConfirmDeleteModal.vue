<template>
  <div class="modal-backdrop" @click="close">
    <div class="modal-content" @click.stop>
      <p>Are you sure you want to delete <strong>{{ dog?.name }}</strong>?</p>
      <button @click="confirm">Yes, Delete</button>
      <button @click="close">Cancel</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import '../assets/styles/DogFormModal.css'

import type { Dog } from '../interfaces/Dog'
import { defineProps, defineEmits } from 'vue'

const props = defineProps<{ dog?: Dog }>()
const emit = defineEmits<{ (e: 'confirm'): void; (e: 'close'): void }>()

const confirm = () => emit('confirm')
const close = () => emit('close')
</script>
