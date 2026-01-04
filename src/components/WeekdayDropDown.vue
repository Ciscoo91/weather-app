<!-- WeekdayDropdown.vue -->
<script setup lang="ts">
import { ref } from 'vue'
import DropdownList from '@/components/DropdownList.vue'
import Button from './Button.vue'

import iconDropdown from '@/assets/images/icon-dropdown.svg'
import type { DropdownItem } from '@/types'

const days = [
  { label: 'Lundi', value: 'mon' },
  { label: 'Mardi', value: 'tue' },
  { label: 'Mercredi', value: 'wed' },
  { label: 'Jeudi', value: 'thu' },
  { label: 'Vendredi', value: 'fri' },
  { label: 'Samedi', value: 'sat' },
  { label: 'Dimanche', value: 'sun' },
]

const selectedDay = ref<DropdownItem | null>({ label: 'Lundi', value: 'mon' })
const open = ref(false)

const handleSelect = (item: DropdownItem) => {
  selectedDay.value = item
  open.value = false
}

const handleClick = () => {
  open.value = !open.value
  console.log('Dropdown open:', open.value)
}

</script>

<template>
  <div>
    <Button 
      variant="primary"
      :icon="iconDropdown" 
      icon-position="right"
      @click="handleClick">{{ selectedDay?.label }}</Button>
  </div>
  <DropdownList
    :selected="selectedDay?.value"
    :items="days"
    :visible="open"
    @select="handleSelect"
    @request-close="open = false"
  />
</template>
