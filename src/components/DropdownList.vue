<template>
  <div class="relative inline-block w-full" ref="root" @keydown.stop.prevent="onKeydown" :aria-expanded="open ? 'true' : 'false'">
    <!-- Trigger / Input -->
    <div class="flex items-center gap-2">
      <!-- <input
        v-if="searchable"
        v-model="query"
        :placeholder="placeholder"
        class="w-full rounded-md border border-gray-300 px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-500"
        @focus="open = true"
        @input="emitSearch()"
        role="combobox"
        :aria-controls="listId"
        :aria-activedescendant="activeId"
      /> -->
      <Button
        type="button"
        class=""
        @click="toggle"
      >
        {{ placeholder }}
      </Button>
    </div>

    <!-- Panel -->
    <ul
      v-show="open"
      class="absolute z-50 mt-2 w-[214px] max-w-[214px] rounded-lg border border-gray-200 bg-neutral-800 shadow-lg scrollbar-hidden divide-y-2 divide-neutral-700"
      role="listbox"
      :id="listId"
      :style="{ maxHeight, overflow: 'auto' }"
    >
      <!-- Liste groupée -->
      <template v-if="grouped">
        <li v-for="(groupItems, groupKey) in groupedMap" :key="groupKey" class="w-[93%] mx-auto py-2">
          <div class="sticky top-0 backdrop-blur px-4 py-2 text-xs font-semibold text-neutral-300 ">
            {{ groupLabel(groupKey) }}
          </div>
          <button
            v-for="(item, idx) in groupItems"
            :key="itemKey(item, idx)"
            class="flex w-full items-center justify-between px-2 py-2 text-left hover:bg-neutral-700 rounded-md"
            :class="{
              'text-blue-700': isSelected(item)
            }"
            :id="optionId(flatIndex(item))"
            role="option"
            :aria-selected="isSelected(item)"
            @mouseenter="highlightedIndex = flatIndex(item)"
            @click="select(item)"
          >
            <slot name="item" :item="item">
              <span class="truncate text-neutral-0 px-2">{{ item[labelKey] }}</span>
              <img v-if="isSelected(item)" :src="iconCheckmark" alt="check-mark icon pr-4" />
            </slot>
          </button>
        </li>
      </template>

      <!-- Liste simple -->
      <template v-else>
        <li
          v-for="(item, idx) in visibleItems"
          :key="itemKey(item, idx)"
          class="flex justify-between w-[93%] mx-auto items-center gap-2 mx-auto my-2 px-2 py-2 text-left hover:bg-neutral-700 rounded-md"
          :class="{}"
          :id="optionId(idx)"
          role="option"
          :aria-selected="isSelected(item)"
          @mouseenter="highlightedIndex = idx"
          @click="select(item)"
        >
          <slot name="item" :item="item">
            <span class="truncate text-neutral-0">{{ item[labelKey] }}</span>
            <img v-if="isSelected(item)" :src="iconCheckmark" alt="check-mark icon pr-4" />
          </slot>
        </li>
      </template>

      <div v-if="visibleItems.length === 0" class="px-3 py-3 text-sm text-neutral-0">
        {{ emptyText }}
      </div>
    </ul>
  </div>
</template>

<script setup lang="ts">
import { computed, onMounted, onBeforeUnmount, ref, watch } from 'vue'
import Button from './Button.vue'
import iconCheckmark from '../assets/images/icon-checkmark.svg'

/** Props */
const props = defineProps({
  modelValue: { type: [String, Number, Object, null], default: null },
  items: { type: Array, default: () => [] },           // [{ label, value, group? }, ...]
  grouped: { type: Boolean, default: false },
  labelKey: { type: String, default: 'label' },
  valueKey: { type: String, default: 'value' },
  groupKey: { type: String, default: 'group' },
  groups: { type: Object, default: () => ({}) },       // { groupId: 'Libellé du groupe' }
  searchable: { type: Boolean, default: false },
  placeholder: { type: String, default: 'Sélectionner…' },
  emptyText: { type: String, default: 'Aucun résultat' },
  maxHeight: { type: String, default: '260px' },
  highlightFirst: { type: Boolean, default: true },
})

const emit = defineEmits(['update:modelValue', 'select', 'open', 'close', 'search'])

/** État */
const open = ref(false)
const query = ref('')
const highlightedIndex = ref(-1)
const root = ref(null)
const listId = `list-${Math.random().toString(36).slice(2)}`
const optionId = (i) => `opt-${listId}-${i}`
const activeId = computed(() => (highlightedIndex.value >= 0 ? optionId(highlightedIndex.value) : undefined))

/** Items filtrés */
const normalized = (s) => (s ?? '').toString().toLowerCase()
const filteredItems = computed(() => {
  if (!props.searchable || !query.value) return props.items
  const q = normalized(query.value)
  return props.items.filter((it) => normalized(it[props.labelKey]).includes(q))
})

/** Grouping helpers */
const groupedMap = computed(() => {
  if (!props.grouped) return {}
  const map = {}
  for (const it of filteredItems.value) {
    const g = it[props.groupKey] ?? '_'
    if (!map[g]) map[g] = []
    map[g].push(it)
  }
  return map
})
const flatList = computed(() => (props.grouped ? Object.values(groupedMap.value).flat() : filteredItems.value))
const visibleItems = computed(() => (props.grouped ? flatList.value : filteredItems.value))
const groupLabel = (key) => props.groups?.[key] ?? key

/** Sélection courante */
const isSelected = (item) =>
  props.modelValue != null &&
  (props.modelValue?.[props.valueKey] ?? props.modelValue) === item[props.valueKey]

const selectedLabel = computed(() => {
  const found = flatList.value.find(isSelected)
  return found ? found[props.labelKey] : (typeof props.modelValue === 'object' ? props.modelValue?.[props.labelKey] : null)
})

/** Indices */
const itemKey = (item, idx) => item[props.valueKey] ?? idx
const flatIndex = (item) => flatList.value.findIndex((x) => x === item)

/** Open/close */
const openMenu = () => {
  if (open.value) return
  open.value = true
  emit('open')
  if (props.highlightFirst && visibleItems.value.length) highlightedIndex.value = 0
}
const closeMenu = () => {
  if (!open.value) return
  open.value = false
  emit('close')
}
const toggle = () => (open.value ? closeMenu() : openMenu())

/** Sélection */
const select = (item) => {
  emit('update:modelValue', item)
  emit('select', item)
  closeMenu()
}

/** Recherche asynchrone (debounce) */
let t = null
const emitSearch = () => {
  if (!props.searchable) return
  if (t) clearTimeout(t)
  t = setTimeout(() => emit('search', query.value), 200)
}

/** Clavier */
const onKeydown = (e) => {
  if (!open.value && ['ArrowDown', 'Enter', ' '].includes(e.key)) {
    openMenu()
    return
  }
  if (!open.value) return
  if (e.key === 'ArrowDown') {
    highlightedIndex.value = Math.min(highlightedIndex.value + 1, visibleItems.value.length - 1)
  } else if (e.key === 'ArrowUp') {
    highlightedIndex.value = Math.max(highlightedIndex.value - 1, 0)
  } else if (e.key === 'Enter') {
    const item = visibleItems.value[highlightedIndex.value]
    if (item) select(item)
  } else if (e.key === 'Escape' || e.key === 'Tab') {
    closeMenu()
  }
}

/** Clic extérieur */
const onClickOutside = (e) => {
  if (!root.value) return
  if (!root.value.contains(e.target)) closeMenu()
}
onMounted(() => document.addEventListener('click', onClickOutside))
onBeforeUnmount(() => document.removeEventListener('click', onClickOutside))

/** Sync quand la liste change */
watch(visibleItems, () => {
  if (props.highlightFirst && open.value) highlightedIndex.value = visibleItems.value.length ? 0 : -1
})
</script>
