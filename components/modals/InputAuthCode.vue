<script setup>
import { ref, reactive, nextTick } from 'vue';
import UIButton from '../ui/UIButton.vue';
import Popper from 'vue3-popper';
import { X } from 'lucide-vue-next';

const emit = defineEmits(['complete']);

const code = reactive(['', '', '', '', '', '']);
const inputRefs = ref([]);

const onInput = (e, index) => {
  const value = e.target.value;
  if (!/^\d$/.test(value)) {
    code[index] = '';
    return;
  }

  code[index] = value;

  if (index < 5) {
    nextTick(() => inputRefs.value[index + 1]?.focus());
  } else {
    const completeCode = code.join('');
    emit('complete', completeCode);
  }
};

const onBackspace = (e, index) => {
  if (code[index] === '' && index > 0) {
    nextTick(() => inputRefs.value[index - 1]?.focus());
  }
};

defineProps({
  onCancel: { type: Function, required: true },
  mandatoryInput: Boolean,
});
</script>
<template>
  <div
    class="fixed inset-0 z-50 flex items-center justify-center bg-opacity-50"
    @click.self="!mandatoryInput && onCancel()"
  >
    <div class="modal w-full max-w-md">
      <div class="relative">
        <!-- X button in top-right -->
        <Popper v-if="!mandatoryInput" content="Cancel" placement="top" hover>
          <UIButton
            :icon="tick"
            :aria-label="cancelLabel"
            @click="onCancel"
            class="absolute to right-2"
          />
        </Popper>

        <!-- Title centered -->
        <h1 class="text-heading">Input your authenticator code</h1>
        <div class="line"></div>

        <div class="flex gap-4 justify-center">
          <input
            v-for="(digit, index) in code"
            :key="index"
            ref="inputRefs"
            v-model="code[index]"
            type="text"
            maxlength="1"
            inputmode="numeric"
            class="w-10 h-10 text-center text-lg"
            @input="onInput($event, index)"
            @keydown.backspace="onBackspace($event, index)"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
input[type='number'] {
  -moz-appearance: textfield;
}
</style>
