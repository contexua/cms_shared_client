<script setup>
import { computed } from 'vue';
import { RouterLink } from 'vue-router';
import * as LucideIcons from 'lucide-vue-next';
import Popper from 'vue3-popper';

const props = defineProps({
  icon: String,
  imgSrc: String,
  to: String,
  href: String,
  as: String,
  ariaLabel: String,
  disabled: Boolean,
  popperContent: String,
});

const Tag = computed(() => {
  if (props.as === 'a') return 'a';
  if (props.to) return RouterLink;
  return 'button';
});

const iconComponent = computed(() =>
  props.icon ? LucideIcons[props.icon] : null
);
</script>

<template>
  <component
    :is="Tag"
    :to="to"
    :href="href"
    :aria-label="ariaLabel"
    :disabled="Tag !== 'a' ? disabled : undefined"
    class="w-6 h-6 flex items-center justify-center rounded-full bordered overflow-hidden transition hover:font-bold"
  >
    <Popper
      v-if="imgSrc"
      :content="popperContent || 'Tooltip'"
      placement="top"
      hover
      class="image-popper"
    >
      <img :src="imgSrc" alt="" class="w-full h-full object-cover" />
    </Popper>
    <component v-else-if="iconComponent" :is="iconComponent" class="w-4 h-4" />
    <slot v-else />
  </component>
</template>

<style>
/* Style specifically for the image popper */
.image-popper .popper {
  background-color: var(--popper-theme-background-color) !important;
  color: var(--popper-theme-text-color) !important;
  padding: var(--popper-theme-padding) !important;
  border-radius: var(--popper-theme-border-radius) !important;
  border-width: var(--popper-theme-border-width) !important;
  border-style: var(--popper-theme-border-style) !important;
  border-color: var(--popper-theme-border-color) !important;
  box-shadow: var(--popper-theme-box-shadow) !important;
  z-index: var(--popper-z-index) !important;
  font-size: 0.875rem !important;
  line-height: 1.25 !important;
  max-width: 200px !important;
}

/* Make sure the image maintains its styles */
.image-popper img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
}
</style>
