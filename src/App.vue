<script setup lang="ts">


import SearchInput from './components/SearchInput.vue';
import WeeklyDropdown from './components/WeekdayDropDown.vue';
import UnitDropdowns from './components/UnitDropdowns.vue';


</script>

<template>
  <div class="flex  justify-content items-center w-1/2 m-auto gap-4">
    <div class="flex flex-col relative ">
      <WeeklyDropdown />
    </div>
    <div class=" flex flex-col relative bg-green-200">
      <UnitDropdowns />
    </div>
  </div>
</template>

<style scoped></style>
