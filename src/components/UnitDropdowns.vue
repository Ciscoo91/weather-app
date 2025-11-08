<!-- UnitDropdowns.vue -->
<script setup>
import { ref } from 'vue'
import DropdownList from '@/components/DropdownList.vue'

const groups = {
  temp: 'Température',
  wind: 'Vitesse du vent',
  rain: 'Précipitations'
}

const unitItems = [
  // Température
  { label: 'Celsius (°C)', value: 'c', group: 'temp' },
  { label: 'Fahrenheit (°F)', value: 'f', group: 'temp' },
  { label: 'Kelvin (K)', value: 'k', group: 'temp' },

  // Vent
  { label: 'm/s', value: 'ms', group: 'wind' },
  { label: 'km/h', value: 'kmh', group: 'wind' },
  { label: 'mph', value: 'mph', group: 'wind' },
  { label: 'nœuds (kn)', value: 'kn', group: 'wind' },

  // Précipitations
  { label: 'millimètres (mm)', value: 'mm', group: 'rain' },
  { label: 'centimètres (cm)', value: 'cm', group: 'rain' },
  { label: 'pouces (in)', value: 'in', group: 'rain' },
]

const selectedUnit = ref(null)
</script>

<template>
  <label class="mb-1 block text-sm font-medium">Unités météo</label>
  <DropdownList
    v-model="selectedUnit"
    :items="unitItems"
    :groups="groups"
    grouped
    placeholder="Choisir une unité…"
  />

  <p class="mt-3 text-sm">
    Selection: <strong>{{ selectedUnit?.label ?? '—' }}</strong>
  </p>
</template>
