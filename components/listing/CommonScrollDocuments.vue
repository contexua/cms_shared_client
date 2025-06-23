<script setup>
import {
  ChevronFirst,
  ChevronLeft,
  ChevronRight,
  ChevronLast,
} from 'lucide-vue-next';
import UIButton from '../ui/UIButton.vue';
import Popper from 'vue3-popper';

defineProps({
  page: Number,
  pageCount: Number,
  totalItemsFetched: Number,
  totalItemsAvailable: Number,
  showAll: Boolean,
});
</script>

<template>
  <div class="flex items-center justify-between w-full text-sm rounded-md">
    <!-- Page Info & Fetched Count -->
    <div class="text-info flex items-center gap-3">
      <span v-if="!showAll">Page {{ page }} of {{ pageCount }}</span>
      <span v-else>Showing all rows</span>

      <span class="text-sm rounded-full">
        {{ totalItemsFetched }} of {{ totalItemsAvailable }} fetched
      </span>
    </div>

    <!-- Pagination Controls -->
    <div v-if="!showAll" class="flex gap-4">
      <Popper content="First page" placement="top" hover>
        <UIButton
          aria-label="First page"
          :disabled="page === 1"
          @click="$emit('update:page', 1)"
          class="button"
        >
          <ChevronFirst class="w-4 h-4" />
        </UIButton>
      </Popper>

      <Popper content="Previous page" placement="top" hover>
        <UIButton
          aria-label="Previous page"
          :disabled="page === 1"
          @click="$emit('update:page', page - 1)"
          class="button"
        >
          <ChevronLeft class="w-4 h-4" />
        </UIButton>
      </Popper>

      <Popper content="Next page" placement="top" hover>
        <UIButton
          aria-label="Next page"
          :disabled="page === pageCount"
          @click="$emit('update:page', page + 1)"
          class="button"
        >
          <ChevronRight class="w-4 h-4" />
        </UIButton>
      </Popper>

      <Popper content="Last page" placement="top" hover>
        <UIButton
          aria-label="Last page"
          :disabled="page === pageCount"
          @click="$emit('update:page', pageCount)"
          class="button"
        >
          <ChevronLast class="w-4 h-4" />
        </UIButton>
      </Popper>
    </div>
  </div>
</template>
