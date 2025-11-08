<!-- CitySearchDropdown.vue -->
<script setup>
import { ref } from 'vue'
import DropdownList from '@/components/DropdownList.vue'

const selectedCity = ref(null)
const cities = ref([])          // résultats dynamiques
const loading = ref(false)

async function fetchCities(q) {
  // Exemple : remplacer par ton appel API (ex: /api/cities?q=...)
  loading.value = true
  await new Promise(r => setTimeout(r, 250)) // simulation réseau
  const ALL = ['Paris', 'Lyon', 'Marseille', 'Bordeaux', 'Toulouse', 'Lille', 'Nantes', 'Nice', 'Rennes', 'Strasbourg']
  cities.value = ALL
    .filter(c => c.toLowerCase().includes(q.toLowerCase()))
    .map(c => ({ label: c, value: c }))
  loading.value = false
}
</script>

<template>
  <label class="mb-1 block text-sm font-medium">Ville</label>
  <DropdownList
    v-model="selectedCity"
    :items="cities"
    searchable
    placeholder="Tape un nom de ville…"
    empty-text="Aucune ville"
    @search="fetchCities"
  />
  <p class="mt-2 text-sm text-gray-600" v-if="loading">Recherche…</p>

  <p class="mt-3 text-sm">
    Selection: <strong>{{ selectedCity?.label ?? '—' }}</strong>
  </p>
</template>
