<script setup>
import { ref, computed, watch, h } from 'vue';
import Popper from 'vue3-popper';
import { LucideLock } from 'lucide-vue-next';
import UIButton from '../ui/UIButton.vue';
import UIPilDisplay from '../ui/UIPilDisplay.vue';

const props = defineProps({
  columns: Array,
  selected: Array,
  colls: Array,
  items: Array,
  hasView: Boolean,
  hasBasic: Boolean,
  hasAdmin: Boolean,
  showAll: Boolean,
  formatDate: Function,
  arrayAttributeClasses: Array,
});

console.log('in list ', props.arrayAttributeClasses);

const emit = defineEmits(['cell-click', 'update:selected']);

const tableFilterValues = ref({});

const displayedColumns = computed(() => {
  if (!Array.isArray(props.colls) || props.colls.length === 0) {
    return props.columns || [];
  }
  return (props.columns || []).filter((col) => props.colls.includes(col.key));
});

const getValue = (obj, path) =>
  path.split('.').reduce((acc, part) => acc?.[part], obj);

const initializeTableFilterValues = () => {
  const initial = {};
  displayedColumns.value.forEach((col) => {
    if (col.type === 'input' || col.type === 'select') {
      initial[col.key] = '';
    }
  });
  tableFilterValues.value = initial;
};

watch(displayedColumns, initializeTableFilterValues, { immediate: true });

const updateTableFilter = (key, val) => {
  tableFilterValues.value[key] = val;
};

const clearTableFilters = () => {
  Object.keys(tableFilterValues.value).forEach((key) => {
    tableFilterValues.value[key] = '';
  });
  emit('update:selected', []);
};

const visibleRows = computed(() => {
  return props.items.filter((item) => {
    return Object.entries(tableFilterValues.value).every(([key, val]) => {
      if (!val) return true;
      const itemVal = getValue(item, key);
      if (Array.isArray(itemVal)) return itemVal.includes(val);
      return (
        typeof itemVal === 'string' &&
        itemVal.toLowerCase().includes(val.toLowerCase())
      );
    });
  });
});

const toggleRowSelection = (id) => {
  const newSelection = props.selected.includes(id)
    ? props.selected.filter((x) => x !== id)
    : [...props.selected, id];
  emit('update:selected', newSelection);
};

const toggleAllVisibleRows = () => {
  const visibleIds = visibleRows.value.map((item) => item._id);
  const allSelected = visibleIds.every((id) => props.selected.includes(id));

  const newSelection = allSelected
    ? props.selected.filter((id) => !visibleIds.includes(id))
    : Array.from(new Set([...props.selected, ...visibleIds]));

  emit('update:selected', newSelection);
};

const handleCellClick = (item) => {
  if (props.hasView) emit('cell-click', item);
};
</script>

<template>
  <div>
    <table class="table">
      <thead>
        <tr>
          <th v-for="col in displayedColumns" :key="col.key">
            <div>{{ col.label }}</div>

            <div v-if="col.type === 'input'">
              <input
                type="text"
                :placeholder="`Filter ${col.label}`"
                :value="tableFilterValues[col.key]"
                @input="(e) => updateTableFilter(col.key, e.target.value)"
                class="input-field"
              />
            </div>

            <div v-else-if="col.type === 'select'">
              <select
                :value="tableFilterValues[col.key]"
                @change="(e) => updateTableFilter(col.key, e.target.value)"
                class="input-select"
              >
                <option value="">All</option>
                <option
                  v-for="(label, key) in col.options"
                  :key="key"
                  :value="key"
                >
                  {{ label }}
                </option>
              </select>
            </div>
          </th>

          <th v-if="hasAdmin" class="text-right">
            <div class="flex items-center gap-4">
              <input
                type="checkbox"
                @change="toggleAllVisibleRows"
                class="input-check"
              />
              <Popper content="Reset filters" placement="top" hover>
                <UIButton
                  aria-label="Reset filters"
                  icon="RotateCcw"
                  @click="clearTableFilters"
                  class="button"
                />
              </Popper>
            </div>
          </th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="item in visibleRows" :key="item._id">
          <td
            v-for="col in displayedColumns"
            :key="col.key"
            class="align-top text-sm"
            :class="{
              'whitespace-nowrap': !col.wrap,
              'whitespace-normal break-words': col.wrap,
            }"
          >
            <span v-if="col.key === 'createdAt'">
              {{ formatDate(item[col.key]) }}
            </span>

            <UIPilDisplay
              v-else-if="Array.isArray(getValue(item, col.key))"
              :available-pils="getValue(item, col.key)"
              :colourPils="arrayAttributeClasses"
            />
            <span v-else-if="typeof col.format === 'function'">
              <component
                v-if="
                  typeof col.format(getValue(item, col.key), item) === 'object'
                "
                :is="col.format(getValue(item, col.key), item).icon"
                :class="[
                  'inline-block w-5 h-5',
                  col.format(getValue(item, col.key), item).color || '',
                ]"
              />
              <span v-else>
                {{ col.format(getValue(item, col.key), item) || '—' }}
              </span>
            </span>

            <span
              v-else-if="col.clickToEdit"
              @click="handleCellClick(item)"
              class="font-medium cursor-pointer hover:underline"
              role="button"
              tabindex="0"
            >
              {{ getValue(item, col.key) || '—' }}
            </span>

            <span v-else>
              {{
                col.options?.[getValue(item, col.key)] ||
                getValue(item, col.key) ||
                '—'
              }}
            </span>
          </td>

          <td v-if="hasAdmin" class="text-right flex">
            <div v-if="item.isLocked" class="justify-end">
              <LucideLock size="16" />
            </div>
            <input
              v-else
              type="checkbox"
              :checked="selected.includes(item._id)"
              @change="() => toggleRowSelection(item._id)"
              class="input-check"
            />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
