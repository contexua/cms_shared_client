<script setup>
import { ref, watch } from 'vue';
import UIButton from '@/components/UIButton.vue';
import Popper from 'vue3-popper';

const props = defineProps({
  fields: Array, // [{ label, value }]
  collection: String,
});
const emit = defineEmits(['search']);

const input = ref('');
const selected = ref('');

const emitSearch = () => {
  emit('search', {
    field: selected.value,
    value: input.value,
  });
};
</script>
<template>
  <div class="flex items-center gap-4">
    <!-- Search input -->
    <h1 class="text-heading whitespace-nowrap">{{ collection }}</h1>

    <input v-model="input" placeholder="Search..." class="input-field" />

    <div class="flex items-center gap-4">
      <!-- Radio buttons -->
      <label v-for="option in fields" :key="option.value" class="text-label">
        <input
          type="radio"
          name="searchField"
          :value="option.value"
          v-model="selected"
          @change="emitSearch"
          class="input-radio mr-2"
        />
        {{ option.label }}
      </label>

      <!-- Search trigger button -->
      <Popper content="Replay search" placement="top" hover>
        <UIButton
          icon="Search"
          aria-label="Search"
          @click="emitSearch"
          class="button"
        />
      </Popper>
    </div>
  </div>
</template>
