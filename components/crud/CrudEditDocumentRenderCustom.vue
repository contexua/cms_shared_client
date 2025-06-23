<script setup>
import { ref, watch } from 'vue';
import UIButton from '@/components/UIButton.vue';
import { RotateCcw, ImageUp } from 'lucide-vue-next';

const props = defineProps({
  field: Object,
  modelValue: String,
});

const emit = defineEmits(['update:modelValue']);

const fileInput = ref(null);
const localValue = ref(props.modelValue);

watch(localValue, (val) => {
  emit('update:modelValue', val);
});

const triggerUpload = () => {
  fileInput.value?.click();
};

const handleUpload = (e) => {
  const file = e.target.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = () => {
    localValue.value = reader.result;
  };
  reader.readAsDataURL(file);
};

const getAvatar = () => {
  // Hardcoded version of getAvatar
  const seed = Math.ceil(Math.random() * 70);
  localValue.value = `https://i.pravatar.cc/150?img=${seed}`;
};

watch(
  () => props.modelValue,
  (newVal) => {
    localValue.value = newVal;
  }
);
</script>

<template>
  <div class="flex flex-col items-start gap-4">
    <label class="text-label">
      {{ field.label }}
    </label>

    <div class="flex flex-col items-center gap-4">
      <img
        :src="localValue || '/images/default-avatar.png'"
        class="aspect-square w-full max-w-[96px] rounded-full object-cover border"
        alt="Preview"
      />

      <div class="flex gap-4">
        <UIButton
          v-if="field.allowGenerate"
          icon="RotateCcw"
          @click="getAvatar"
          class="button"
        />
        <UIButton
          v-if="field.allowUpload"
          icon="ImageUp"
          @click="triggerUpload"
          class="button"
        />
      </div>

      <input
        ref="fileInput"
        type="file"
        accept="image/*"
        class="hidden"
        @change="handleUpload"
      />
    </div>
  </div>
</template>
