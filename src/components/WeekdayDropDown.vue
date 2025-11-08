<!-- WeekdayDropdown.vue -->
<script setup>
import { ref } from 'vue'
import DropdownList from '@/components/DropdownList.vue'

const days = [
  { label: 'Lundi', value: 'mon' },
  { label: 'Mardi', value: 'tue' },
  { label: 'Mercredi', value: 'wed' },
  { label: 'Jeudi', value: 'thu' },
  { label: 'Vendredi', value: 'fri' },
  { label: 'Samedi', value: 'sat' },
  { label: 'Dimanche', value: 'sun' },
]

const selectedDay = ref(null)
</script>

<template>
  <label class="mb-1 block text-sm font-medium">Jour</label>
  <DropdownList
    v-model="selectedDay"
    :items="days"
    placeholder="Choisir un jour…"
  />
  <p class="mt-3 text-sm">
    Selection: <strong>{{ selectedDay?.label ?? '—' }}</strong>
  </p>
</template>
