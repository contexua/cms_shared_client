<script setup>
import { Plus, Trash2, CircleEllipsisIcon, Expand } from 'lucide-vue-next';
import Popper from 'vue3-popper';
import UIButton from '../ui/UIButton.vue';

defineProps({
  selected: {
    type: Array,
    required: true,
  },
  hasView: Boolean,
  hasBasic: Boolean,
  hasAdmin: Boolean,
  showAll: {
    type: Boolean,
    default: false,
  },
  // swithc on or off some features
  // showOptionInfo: {
  //   type: Boolean,
  //   default: true,
  // },
});

defineEmits(['add', 'batch', 'delete', 'toggle-view', 'toggle-info']);
</script>

<template>
  <div class="flex items-center gap-4">
    <Popper content="Add new item" placement="top" hover>
      <UIButton
        icon="Plus"
        aria-label="Add"
        :disabled="!hasBasic"
        @click="$emit('add')"
        class="button"
      />
    </Popper>

    <Popper
      :content="selected.length === 0 ? 'Select rows first' : 'Batch Edit'"
      placement="top"
      hover
    >
      <UIButton
        icon="CircleEllipsis"
        aria-label="Batch Edit"
        :disabled="!hasAdmin || selected.length === 0"
        @click="$emit('batch')"
        class="button"
      />
    </Popper>

    <Popper
      :content="selected.length === 0 ? 'Select rows first' : 'Delete Selected'"
      placement="top"
      hover
    >
      <UIButton
        icon="Trash2"
        aria-label="Delete"
        :disabled="!hasAdmin || selected.length === 0"
        @click="$emit('delete')"
        class="button"
      />
    </Popper>

    <Popper
      :content="showAll ? 'Show Paginated' : 'Show All Rows'"
      placement="top"
      hover
    >
      <UIButton
        icon="Expand"
        aria-label="Toggle View Mode"
        @click="$emit('toggle-view')"
        class="button"
      />
    </Popper>
    <Popper content="How to use this form" placement="top" hover>
      <UIButton
        icon="Info"
        aria-label="Help"
        @click="$emit('toggle-info')"
        class="button"
      />
    </Popper>
  </div>
</template>
