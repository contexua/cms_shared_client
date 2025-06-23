<script setup>
import { ref } from 'vue';
import UIButton from '@/components/UIButton.vue';

const props = defineProps({ onConfirm: Function, onCancel: Function });
const name = ref('');
const description = ref('');

function confirm() {
  props.onConfirm?.({ name: name.value, description: description.value });
}
</script>

<template>
  <div
    class="fixed inset-0 z-50 bg-opacity-40 flex items-center justify-center"
    @click.self="onCancel"
  >
    <div class="modal w-full max-w-md">
      <h1 class="text-heading">Bookmark Item</h1>
      <div class="line"></div>

      <div class="space-y-4">
        <div>
          <label class="text-label">Name</label>
          <input
            v-model="name"
            class="input-field"
            placeholder="Bookmark name"
          />
        </div>
        <div>
          <label class="text-label">Description</label>
          <textarea
            v-model="description"
            class="input-field"
            placeholder="Optional description"
          />
        </div>
      </div>

      <div class="flex justify-end gap-4">
        <UIButton icon="X" @click="onCancel" class="button" />
        <UIButton
          icon="Check"
          :disabled="!name"
          @click="confirm"
          class="button"
        />
      </div>
    </div>
  </div>
</template>
