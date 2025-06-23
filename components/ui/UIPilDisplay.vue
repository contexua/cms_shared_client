<!-- UIPilDisplay.vue -->
<script setup>
import { toRefs, computed } from 'vue';

const props = defineProps({
  /** Array of strings, e.g. ['alpha','final','wip'] */
  availablePils: {
    type: Array,
    default: () => [],
  },
  /** Array of [pilName, className] pairs, e.g.
   *  [['final','base-text-pil-label-ds'], ['wip','base-text-pil-label-wip']]
   */
  colourPils: {
    type: Array,
    default: () => [],
  },
});
const { availablePils, colourPils } = toRefs(props);

/** Build a lookup map from the pairs */
const pilClassMap = computed(() => {
  return colourPils.value.reduce((map, [name, cls]) => {
    map[name] = cls;
    return map;
  }, {});
});
</script>

<template>
  <div class="flex flex-wrap gap-4">
    <label
      v-for="pil in availablePils"
      :key="pil"
      :class="[
        'pill' /* always apply base styling */,
        pilClassMap[pil] || '' /* add variant if defined */,
      ]"
    >
      {{ pil }}
    </label>
  </div>
</template>
