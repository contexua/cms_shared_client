<!-- src/components/PilPicker.vue -->
<script setup>
import { toRefs, computed } from 'vue';

const props = defineProps({
  /** { [key: string]: string } or Array<{ value: string; label: string }> */
  availablePils: {
    type: [Object, Array],
    required: true,
  },
  /** v-model binding for the selected pil values */
  modelValue: {
    type: Array,
    default: () => [],
  },
  hasAdmin: {
    type: Boolean,
    default: true,
  },
  colourPils: {
    type: Array,
    default: () => [],
  },
});
const emit = defineEmits(['update:modelValue']);

const { availablePils, modelValue } = toRefs(props);

/** Normalize to array of { value, label } */
const pilList = computed(() => {
  if (Array.isArray(availablePils.value)) {
    return availablePils.value;
  }
  return Object.entries(availablePils.value).map(([value, label]) => ({
    value,
    label,
  }));
});
const pilClassMap = computed(() =>
  props.colourPils.reduce((map, [name, cls]) => {
    map[name] = cls;
    return map;
  }, {})
);

function togglePil(pilValue, checked) {
  let next = [];
  if (checked) {
    next = [...modelValue.value, pilValue];
  } else {
    next = modelValue.value.filter((v) => v !== pilValue);
  }
  emit('update:modelValue', next);
}
</script>

<template>
  <div>
    <!-- <label class="text-label">Pils</label> -->
    <div class="flex flex-wrap gap-4">
      <label
        v-for="pil in pilList"
        :key="pil.value"
        :class="['pill w-1/3', pilClassMap[pil.value] || '']"
      >
        <input
          type="checkbox"
          :value="pil.value"
          :checked="modelValue.includes(pil.value)"
          @change="(event) => togglePil(pil.value, event.target.checked)"
          :disabled="pil.value === 'final' && !props.hasAdmin"
          class="input-check disabled:opacity-50 disabled:cursor-not-allowed"
        />
        {{ pil.label }}
      </label>
    </div>
  </div>
</template>
