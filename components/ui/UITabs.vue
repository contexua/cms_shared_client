<script setup>
import { defineProps, defineEmits, computed } from 'vue';
import UIButton from './UIButton.vue';
import Popper from 'vue3-popper';

const props = defineProps({
  tabs: { type: Array, required: true }, // each tab: { name, key }
  modelValue: { type: String, default: '' },
  actions: { type: Array, default: () => [] }, // parallel array of { icon, tooltip, onClick }
});
const emit = defineEmits(['update:modelValue']);

const activeKey = computed({
  get: () => props.modelValue || props.tabs[0]?.key,
  set: (k) => emit('update:modelValue', k),
});
</script>

<template>
  <div>
    <div class="flex items-center">
      <div class="flex items-center space-x-4">
        <template v-for="(tab, i) in tabs" :key="tab.key">
          <button
            @click="activeKey = tab.key"
            :class="['tab', activeKey === tab.key ? 'tab-active' : '']"
          >
            <span class="text-info">{{ tab.name }}</span>
          </button>
          <Popper
            v-if="actions[i]"
            :content="actions[i].tooltip"
            placement="top"
            hover
          >
            <UIButton
              :icon="actions[i].icon"
              @click="actions[i].onClick"
              class="button"
            />
          </Popper>
        </template>
      </div>
    </div>

    <!-- Render only the active slot -->
    <div class="mt-4">
      <slot :name="activeKey" />
    </div>
  </div>
</template>
