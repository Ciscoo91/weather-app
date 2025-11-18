<script setup lang="ts">

import {ref} from 'vue';
import Button from './components/Button.vue';
import SearchInput from './components/SearchInput.vue';
import WeeklyDropdown from './components/WeekdayDropDown.vue';
import UnitDropdowns from './components/UnitDropdowns.vue';


</script>

<template>
  <SearchInput />
  <WeeklyDropdown />
  <UnitDropdowns />
  <UnitDropdowns />
</template>

<style scoped></style>
