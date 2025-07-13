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
  // colourPils: {
  //   type: Array,
  //   default: () => [],
  // },
  colourPils: {
    type: [Object, Array],
    default: () => ({}),
  },
});
const { availablePils, colourPils } = toRefs(props);

/** Build a lookup map from the pairs */
// const pilClassMap = computed(() => {
//   return colourPils.value.reduce((map, [name, cls]) => {
//     map[name] = cls;
//     return map;
//   }, {});
// });

const pilClassMap = computed(() => {
  const cp = colourPils.value;

  // ✅ Handle array of [key, value] pairs
  if (Array.isArray(cp)) {
    return cp.reduce((map, pair) => {
      // Ensure pair is valid (2-length array)
      if (Array.isArray(pair) && pair.length === 2) {
        const [key, val] = pair;
        map[key] = val;
      }
      return map;
    }, {});
  }

  // ✅ Handle plain object
  if (cp && typeof cp === 'object' && cp.constructor === Object) {
    return cp;
  }

  // 🚨 Anything else, fallback
  console.warn('Invalid colourPils format:', cp);
  return {};
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
