<script>
export default {
  name: 'Alert',
  props: {
    type: { type: String, default: 'info' }, // 'info' | 'error' | 'success'
  },
  computed: {
    typeClasses() {
      return {
        info: 'bg-blue-50 border-blue-200 text-blue-800',
        success: 'bg-green-50 border-green-200 text-green-800',
        error: 'bg-red-50 border-red-200 text-red-800',
      }[this.type];
    },
  },
};
</script>
<template>
  <div :class="`${typeClasses}`">
    <slot />
  </div>
</template>
