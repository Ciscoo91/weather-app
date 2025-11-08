<template>
    <div class="relative w-full">
        <!-- decorative search icon placed inside the input on the left -->
        <img
            :src="searchIcon"
            alt="search icon"
            aria-hidden="true"
            class="absolute left-3 top-1/2 -translate-y-1/2 w-5 h-5 pointer-events-none"
        />

        <input
            name="search-input"
            type="text"
            class="rounded-lg font-normal bg-neutral-700 text-neutral-0 placeholder:text-neutral-0 p-2 pl-10 w-full focus:outline-2 focus:outline-offset-2 focus:outline-blue-2 focus:outline-neutral-0"
            placeholder="Search for a city, e.g., New York"
        />
    </div>
    <div class="relative">
        <input type="text" class="input pl-10" placeholder="Search for a city, e.g., New York">
        <div class="absolute left-3 top-1/2 -translate-y-1/2 pointer-events-none text-muted-foreground [&>svg]:size-4 ">
            <img :src="searchIcon" alt="" srcset="">
        <!-- <svg :xmlns="searchIcon" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="11" cy="11" r="8" /><path d="m21 21-4.3-4.3" /></svg> -->
        </div>
    </div>
</template>

<script setup lang="ts">
import searchIcon from '../assets/images/icon-search.svg'
</script>

<style lang="">
</style>