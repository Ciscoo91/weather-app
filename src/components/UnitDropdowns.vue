<!-- UnitDropdowns.vue -->
<script setup lang="ts">
import { ref } from 'vue'
import DropdownList from '@/components/DropdownList.vue'
import Button from './Button.vue'
import iconUnits from '@/assets/images/icon-units.svg'
import { type DropdownItem, type UnitItemsType } from '@/types'
const groups = {
  temp: 'Température',
  wind: 'Vitesse du vent',
  rain: 'Précipitations'
}

const unitItems : UnitItemsType[] = [
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

const open = ref(false)
const selectedUnit = ref<DropdownItem | null>(null)
const handleClick = () => {
  open.value = !open.value
}

const handleSelect = (item: DropdownItem) => {
  selectedUnit.value = item
  open.value = false
}

</script>

<template>
  <div>
    <Button 
      variant="primary" 
      @click="handleClick()" 
      :icon="iconUnits" 
      icon-position="right">Units</Button>
  </div>
  <DropdownList
    v-model="selectedUnit"
    :items="unitItems"
    group-key="group"
    @select="handleSelect"
    :visible="open"
    placeholder="Choisir une unité…"
  >
    <template #group-label="{ group }">
      <div class="sticky top-0 bg-neutral-800/90 px-3 py-2 text-xs font-semibold text-neutral-300">
        {{ groups[group] ?? group }}
      </div>
    </template>
  </DropdownList>
</template>
