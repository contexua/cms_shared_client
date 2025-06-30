<!-- src/components/CrudIndex.vue -->
<script setup>
import { defineProps, defineEmits } from 'vue';
import Spinner from '../ui/UISpinner.vue';
import Alert from '../ui/UIAlert.vue';
import EmptyState from '../ui/UIEmptyState.vue';
import UIHelpPanel from '@/components/commonapp/UIHelpPanel.vue';

const props = defineProps({
  /** When true, disables interaction and shows a loading spinner */
  loading: { type: Boolean, default: false },

  /** If set, shows an error banner */
  error: { type: Object, default: null },

  /** The list of items to display (for empty-state check) */
  items: { type: Array, default: () => [] },

  /* show the info div */
  showInfoDiv: { type: Boolean, default: false },

  infoTitle: String,
  infoSection: String,
});

// emit retry when user clicks “Retry”
const emit = defineEmits(['retry']);
</script>

<template>
  <div
    class="relative w-full overflow-hidden"
    style="height: 70vh; min-width: 60vw"
  >
    <!-- Spinner Overlay -->
    <div
      v-if="loading"
      class="absolute inset-0 flex items-center justify-center z-10"
    >
      <Spinner />
    </div>

    <!-- Error Banner -->
    <Alert v-if="error" type="error" class="">
      {{ error.message || 'Something went wrong' }}
      <button @click="emit('retry')" class="button">Retry</button>
    </Alert>

    <!-- Toolbar -->
    <div class="flex justify-between items-center gap-4">
      <slot name="search" />
      <slot name="actions" />
    </div>

    <div v-if="!showInfoDiv">
      <!-- Empty State -->
      <EmptyState v-if="!loading && !error && items.length === 0">
        <slot name="empty">No items found.</slot>
      </EmptyState>

      <!-- Body -->
      <div v-else class="overflow-x-auto">
        <div class="max-h-[calc(100vh-12rem)] overflow-y-auto w-full">
          <slot name="body" />
        </div>
      </div>

      <!-- Pagination -->
      <div class="flex items-center justify-between w-full">
        <slot name="pagination" />
      </div>
    </div>
    <div
      v-else
      class="flex flex-1 gap-4 overflow-hidden transition-all duration-300"
    >
      <UIHelpPanel :title="infoTitle" :section="infoSection" />
    </div>
  </div>
</template>
