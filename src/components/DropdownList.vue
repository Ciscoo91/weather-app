<!-- weather-app/src/components/DropdownList.vue -->
<template>
  <transition name="fade">
    <ul
      v-if="visible && visibleItems.length"
      ref="listRef"
      class=" start-0 top-20 z-50 mt-2 w-[214px] rounded-lg border border-neutral-700 bg-neutral-800 text-sm shadow-xl scrollbar-hidden"
      role="listbox"
      :aria-activedescendant="activeId"
      :style="{ maxHeight, overflowY: 'auto' }"
      @keydown.stop.prevent="onKeydown"
    >
      <template v-if="isGrouped">
        <li v-for="(groupItems, group) in groupedItems" :key="group">
          <slot name="group-label" :group="group">
            <div class="sticky top-0 bg-neutral-800/90 px-3 py-2 text-xs font-semibold text-neutral-300">
              {{ group }}
            </div>
          </slot>

          <button
            v-for="(item, idx) in groupItems"
            :key="itemKey(item, idx)"
            class="flex w-full items-center justify-between px-3 py-2 text-left text-neutral-100 hover:bg-neutral-700"
            :class="{ 'text-blue-400': isSelected(item), 'bg-neutral-700': highlightedId === optionId(itemKey(item, idx)) }"
            type="button"
            role="option"
            :id="optionId(itemKey(item, idx))"
            :aria-selected="isSelected(item)"
            @mouseenter="highlight(itemKey(item, idx))"
            @click="select(item)"
          >
            <slot name="item" :item="item" :selected="isSelected(item)">
              <span class="truncate">{{ item[labelKey] }}</span>
            </slot>
          </button>
        </li>
      </template>

      <template
          v-else-if="visible && !visibleItems.length"
          class="absolute z-50  w-full rounded-lg border border-neutral-700 bg-neutral-800 px-3 py-3 text-center text-xs text-neutral-400"
      >
          {{ emptyText }}
      </template>

      <template v-else>
        <button
          v-for="(item, idx) in visibleItems"
          :key="itemKey(item, idx)"
          class="flex w-full items-center justify-between px-3 py-2 text-left text-neutral-100 hover:bg-neutral-700"
          :class="{ 'text-blue-400': isSelected(item), 'bg-neutral-700': highlightedId === optionId(idx) }"
          type="button"
          role="option"
          :id="optionId(idx)"
          :aria-selected="isSelected(item)"
          @mouseenter="highlight(idx)"
          @click="select(item)"
        >
          <slot name="item" :item="item" :selected="isSelected(item)">
            <span class="truncate">{{ item[labelKey] }}</span>
          </slot>
        </button>
      </template>

    </ul>
  </transition>

</template>

<script setup lang="ts">
import { computed, onBeforeUnmount, onMounted, ref, watch } from 'vue'
/* '@/types' does not export DropdownItem in this project, so define a local type here.
   Adjust the shape to match your real item structure if needed. */
import { type DropdownItem } from '@/types'

const props = withDefaults(
  defineProps<{
    items: DropdownItem[]
    visible: boolean
    selected?: string | number | null
    query?: string
    labelKey?: keyof DropdownItem
    valueKey?: keyof DropdownItem
    groupKey?: keyof DropdownItem | null
    maxHeight?: string
    emptyText?: string
  }>(),
  {
    query: '',
    selected: null,
    labelKey: 'label',
    valueKey: 'value',
    groupKey: null,
    maxHeight: '16rem',
    emptyText: 'No result',
  },
)

const emit = defineEmits<{
  select: [DropdownItem]
  'request-close': []
}>()

const listRef = ref<HTMLElement | null>(null)
const optionId = (key: number | string) => `dropdown-opt-${key}`
const highlightedId = ref<string | null>(null)
const activeId = computed<string | undefined>(() => highlightedId.value ?? undefined)

const normalized = (value?: string) => (value ?? '').toString().toLowerCase()
const visibleItems = computed(() => {
  if (!props.query) return props.items
  const needle = normalized(props.query)
  return props.items.filter(item => normalized(item[props.labelKey as string]).includes(needle))
})

const isGrouped = computed(() => Boolean(props.groupKey))
const groupedItems = computed<Record<string, DropdownItem[]>>(() => {
  if (!isGrouped.value) return {}
  const key = props.groupKey as keyof DropdownItem
  return visibleItems.value.reduce((groups, item) => {
    const g = (item[key] ?? '_').toString()
    if (!groups[g]) groups[g] = []
    groups[g].push(item)
    return groups
  }, {} as Record<string, DropdownItem[]>)
})

const itemKey = (item: DropdownItem, idx: number) => (item[props.valueKey as string] ?? idx) as number | string
const isSelected = (item: DropdownItem) => props.selected === item[props.valueKey as string]

const highlight = (key: number | string) => {
  highlightedId.value = optionId(key)
}
const select = (item: DropdownItem) => {
  emit('select', item)
  emit('request-close')
}

const onKeydown = (event: KeyboardEvent) => {
  if (!props.visible || !visibleItems.value.length) return
  const currentIdx = highlightedId.value
    ? visibleItems.value.findIndex(item => optionId(itemKey(item, 0)) === highlightedId.value)
    : -1
  if (event.key === 'ArrowDown') {
    const next = Math.min(currentIdx + 1, visibleItems.value.length - 1)
    highlight(next)
  } else if (event.key === 'ArrowUp') {
    const prev = Math.max(currentIdx - 1, 0)
    highlight(prev)
  } else if (event.key === 'Enter' && currentIdx >= 0) {
    const item = visibleItems.value[currentIdx]
    if (item) select(item)
  } else if (event.key === 'Escape') {
    emit('request-close')
  }
}

const handlePointerDown = (event: MouseEvent) => {
  if (!props.visible || !listRef.value) return
  if (!listRef.value.contains(event.target as Node)) emit('request-close')
}

watch(
  () => props.visible,
  visible => {
    if (!visible) highlightedId.value = null
  },
)

onMounted(() => document.addEventListener('pointerdown', handlePointerDown))
onBeforeUnmount(() => document.removeEventListener('pointerdown', handlePointerDown))
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 120ms ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
