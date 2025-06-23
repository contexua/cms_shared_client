<script setup>
import UIButton from '../ui/UIButton.vue';
import Popper from 'vue3-popper';

defineProps({
  title: { type: String, default: 'Please Confirm' },
  message: { type: String, required: true },
  onConfirm: { type: Function, required: true },
  onCancel: { type: Function, required: true },
  confirmLabel: { type: String, default: 'Confirm' },
  cancelLabel: { type: String, default: 'Cancel' },
  confirmIcon: { type: String, default: 'Check' },
  cancelIcon: { type: String, default: 'X' },
  confirmClass: {
    type: String,
    default: '',
  },
});
</script>

<template>
  <div
    class="fixed inset-0 z-50 flex items-center justify-center bg-opacity-50"
    @click.self="onCancel"
  >
    <div class="modal w-full max-w-md">
      <!-- Title -->
      <div class="flex justify-between items-center gap-4">
        <h1 class="text-heading">{{ title }}</h1>
      </div>
      <div class="line"></div>
      <!-- Message -->
      <p class="text-info text-xl">{{ message }}</p>

      <!-- Buttons -->
      <div class="flex justify-end gap-4">
        <Popper :content="cancelLabel" placement="top" hover>
          <UIButton
            :icon="cancelIcon"
            :aria-label="cancelLabel"
            @click="onCancel"
            class="button"
          />
        </Popper>
        <Popper :content="confirmLabel" placement="top" hover>
          <UIButton
            :icon="confirmIcon"
            :aria-label="confirmLabel"
            @click="onConfirm"
            :class="confirmClass"
            class="button"
          />
        </Popper>
      </div>
    </div>
  </div>
</template>
