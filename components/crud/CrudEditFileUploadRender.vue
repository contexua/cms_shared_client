<script setup>
import { ref, computed, watch } from 'vue';
import { useAssetSpaceStore } from '@/stores/storeDesignerSpaces';

const props = defineProps({
  field: Object,
  modelValue: Object, // ← this is the full workingItem
});
const emit = defineEmits(['update:modelValue']);

const assetspaceStore = useAssetSpaceStore();
const fileInput = ref(null);

const assetType = computed(() => props.modelValue?.assetType || '');
const assetspace = computed(() => assetspaceStore.assetspace);
const showUpload = computed(() =>
  ['image', 'media', 'font', 'script', 'css', 'other'].includes(assetType.value)
);

const triggerUpload = () => fileInput.value?.click();

async function handleUpload(e) {
  const file = e.target.files[0];
  if (!file || !assetspace.value) return;

  const formData = new FormData();
  formData.append('file', file);

  const uploadPath = `/api/systemfiles/assetspaces/${assetspace.value}/uploads/upload`;

  try {
    const res = await fetch(uploadPath, {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${localStorage.getItem('token')}`,
      },
      body: formData,
    });

    const result = await res.json();
    if (result.success) {
      emit('update:modelValue', {
        ...props.modelValue,
        mimeType: file.type,
        fileSize: file.size,
        thumbnailUrl: file.type.startsWith('image') ? result.path : null,
        filePath: result.path, // you can persist this if needed
      });
    } else {
      throw new Error(result.message || 'Upload failed');
    }
  } catch (err) {
    console.error('Upload error', err);
  }
}

watch(
  () => props.modelValue,
  (val) => {
    // Re-initialize if needed
  }
);
</script>

<template>
  <div v-if="showUpload" class="flex flex-col gap-2">
    <label class="text-label">{{ field.label || 'Upload Asset' }}</label>

    <button class="button" @click="triggerUpload">Choose File</button>
    <input
      type="file"
      ref="fileInput"
      class="hidden"
      @change="handleUpload"
      accept="*/*"
    />

    <img
      v-if="props.modelValue?.thumbnailUrl"
      :src="props.modelValue.thumbnailUrl"
      class="mt-2 w-32 h-32 object-cover rounded"
    />
  </div>
</template>
