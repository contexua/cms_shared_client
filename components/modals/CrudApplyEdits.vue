<script setup>
import { ref, watch } from 'vue';
import UIButton from '../ui/UIButton.vue';
import Popper from 'vue3-popper';

const props = defineProps({
  fieldOptions: Array,
  valueOptions: Object,
  initialField: String,
  initialValue: String,
  onConfirm: Function,
  onCancel: Function,
});

const localField = ref(props.initialField || '');
const localValue = ref(props.initialValue || '');

watch(localField, () => {
  localValue.value = '';
});

const emitConfirm = () => {
  props.onConfirm?.({
    field: localField.value,
    value: localValue.value,
  });
};
</script>

<template>
  <div
    class="fixed inset-0 z-50 bg-opacity-40 flex items-center justify-center"
    @click.self="onCancel"
  >
    <!-- max-w-md = limit to 28rem maximum (md) -->
    <div class="modal w-full max-w-md">
      <div class="flex justify-between items-center gap-4">
        <h1 class="text-heading">Batch Edit</h1>
      </div>
      <div class="line"></div>

      <div class="space-y-4">
        <div>
          <label class="text-info text-2xl">Field to Update</label>
          <select v-model="localField" class="input-select w-1/2">
            <option value="">Select field</option>
            <option
              v-for="option in fieldOptions"
              :key="option.value"
              :value="option.value"
            >
              {{ option.label }}
            </option>
          </select>
        </div>

        <!-- Input for password field -->
        <div v-if="localField === 'password'">
          <label class="text-info text-2xl">New Password</label>
          <input
            type="password"
            v-model="localValue"
            class="input-field w-1/2"
            placeholder="Enter new password"
          />
        </div>

        <!-- Dropdown for fields with options -->
        <div v-else-if="localField && valueOptions[localField]">
          <label class="text-info text-2xl">Value</label>
          <select v-model="localValue" class="input-select w-1/2">
            <option
              v-for="(label, key) in valueOptions[localField]"
              :key="key"
              :value="key"
            >
              {{ label }}
            </option>
          </select>
        </div>
      </div>

      <div class="flex justify-end gap-4">
        <Popper content="Cancel edits" placement="top" hover>
          <UIButton
            icon="X"
            aria-label="Cancel"
            @click="onCancel"
            class="button"
          />
        </Popper>
        <Popper content="Apply edits" placement="top" hover>
          <UIButton
            icon="Check"
            aria-label="Confirm"
            @click="emitConfirm"
            :disabled="!localField || !localValue"
            class="button"
          />
        </Popper>
      </div>
    </div>
  </div>
</template>
