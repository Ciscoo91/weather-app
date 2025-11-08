<template>
    <!-- <button class="px-[24px] p-[16px] text-white bg-blue-500 rounded-lg hover:cursor-pointer hover:bg-blue-700 focus:outline-2 focus:outline-offset-2 focus:outline-blue-2 focus:outline-blue-500">
        <slot></slot>
    </button> -->

    <button
        :disabled="disabled"
        :class="[classes.base, classes.size, classes.variant]"
    >
        <!-- Icône left -->
        <component
            v-if="icon && iconPosition === 'left'"
            :is="icon"
            class="w-5 h-5 mr-2"
        />

        <slot />

        <!-- Icône right -->
        <component
            v-if="icon && iconPosition === 'right'"
            :is="icon"
            class="w-5 h-5 ml-2"
        />
    </button>

</template>
<script setup lang="ts">
import {computed, defineProps} from 'vue';

const props = defineProps({
  variant: {
    type: String,
    default: 'primary',
    validator: (v: string) =>
      ['primary', 'secondary', 'outline', 'danger'].includes(v)
  },
  size: {
    type: String,
    default: 'md',
    validator: (s: string) => ['sm', 'md', 'lg'].includes(s)
  },
  icon: {
    type: [Object, Function], // composant icône, ex: import IconX
    default: null
  },
  iconPosition: {
    type: String,
    default: 'left',
    validator: (p: string) => ['left', 'right'].includes(p)
  },
  disabled: Boolean
})

const classes = computed(() => {
  const base =
    'font-grotesque rounded-lg font-medium focus:outline-2 focus:outline-offset-2 disabled:opacity-50 disabled:cursor-not-allowed inline-flex items-center justify-center'

  const sizes: Record<string, string> = {
    sm: 'px-3 py-2 text-sm',
    md: 'px-4 py-2 text-base',
    lg: 'px-5 py-3 text-lg'
  }

  const variants: Record<string, string> = {
    primary:
      'bg-blue-500 text-white hover:bg-blue-700 focus:outline-blue-500',
    secondary:
      'bg-neutral-700 text-white hover:bg-neutral-800 focus:outline-neutral-700',
    outline:
      'bg-transparent border border-neutral-700 text-white hover:bg-neutral-800 focus:outline-neutral-700',
    danger:
      'bg-red-500 text-white hover:bg-red-700 focus:outline-red-500'
  }

  return {
    base,
    size: sizes[props.size],
    variant: variants[props.variant]
  }
})
</script>