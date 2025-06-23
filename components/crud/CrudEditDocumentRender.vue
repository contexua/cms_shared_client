<script setup>
import { computed } from 'vue';
import UIPilEdit from '@/components/UIPilEdit.vue';
import { ChevronDown } from 'lucide-vue-next';

const props = defineProps({
  field: Object,
  modelValue: null,
  hasAdmin: Boolean,
  arrayAttributeClasses: Array,
});
// General disable logic — more flexible 'isLockded && hasAdmin === false -- then disable'
const isDisabled = computed(() => {
  const lockedFields = ['isLocked']; // Add more keys here if needed
  return lockedFields.includes(props.field?.key) && !props.hasAdmin;
});

const emit = defineEmits(['update:modelValue']);

const model = computed({
  get: () => props.modelValue,
  set: (val) => emit('update:modelValue', val),
});

function toggleCheckbox(val) {
  const current = Array.isArray(model.value) ? model.value : [];
  const updated = current.includes(val)
    ? current.filter((v) => v !== val)
    : [...current, val];
  model.value = updated;
}

const formattedReadonlyValue = computed(() => {
  if (props.field.format && typeof props.field.format === 'function') {
    return props.field.format(model.value);
  }
  return model.value ?? '—';
});
</script>

<template>
  <!-- Blank Field -->
  <div
    v-if="field.blank"
    :class="field.class"
    style="height: 0; visibility: hidden"
  >
    &nbsp;
  </div>

  <!-- Render Field -->
  <div v-else-if="field">
    <label v-if="field.label" class="text-label">
      {{ field.label }}
    </label>
    <div class="px-0">
      <!-- Custom Render -->
      <component
        v-if="field.customRender"
        :is="field.customRender"
        :field="field"
        v-model="model"
      />

      <!-- Input -->
      <input
        v-else-if="field.type === 'input'"
        :type="field.inputType || 'text'"
        v-model="model"
        class="input-field"
      />

      <!-- Number -->
      <input
        v-else-if="field.type === 'number'"
        type="number"
        v-model.number="model"
        class="input-field"
      />

      <div v-else-if="field.type === 'select'" class="relative">
        <!-- Select Input -->
        <select
          v-model="model"
          :disabled="
            isDisabled ||
            !field.options ||
            Object.keys(field.options).length === 0
          "
          class="input-select disabled:opacity-50 pr-10 appearance-none w-full"
        >
          <option disabled value="">-- Select --</option>
          <option
            v-for="(label, val) in field.options || {}"
            :key="val"
            :value="val"
          >
            {{ label }}
          </option>
        </select>
      </div>

      <!-- Checkbox Group -->
      <div
        v-else-if="field.type === 'checkbox-group'"
        class="flex flex-wrap gap-4"
      >
        <!-- <label
          v-for="(label, val) in field.options || {}"
          :key="val"
          class="text-label"
        >
          <input
            type="checkbox"
            :value="val"
            :checked="Array.isArray(model) && model.includes(val)"
            @change="toggleCheckbox(val)"
          />
          {{ label }}
        </label> -->

        <UIPilEdit
          :availablePils="field.options"
          :has-admin="true"
          v-model="model"
          :colourPils="arrayAttributeClasses"
        />
      </div>

      <!-- Textarea -->
      <textarea
        v-else-if="field.type === 'textarea'"
        v-model="model"
        :rows="field.rows || 4"
        class="input-field"
      />

      <!-- Readonly -->
      <p v-else-if="field.type === 'readonly'" class="">
        <span class="text-info">{{ formattedReadonlyValue }}</span>
      </p>

      <!-- Fallback -->
      <p v-else class="text-sm text-red-500">
        Unknown field type: {{ field.type }}
      </p>
    </div>
  </div>
</template>
