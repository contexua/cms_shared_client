<script setup>
import { computed, ref, onMounted } from 'vue';
import {
  X,
  Check,
  Copy,
  ChevronLeft,
  ChevronRight,
  ChevronUp,
} from 'lucide-vue-next';
import UIButton from '../ui/UIButton.vue';
import Popper from 'vue3-popper';
import { CrudEditDocumentRender } from 'cms_shared_client';
import { CrudEditDocumentRenderCustom } from 'cms_shared_client';

const props = defineProps({
  modelValue: Object,
  isCreating: Boolean,
  title: String,
  fields: Array,
  editIndexInfo: Object,
  itemEditLayout: Array,
  hasView: Boolean,
  hasBasic: Boolean,
  hasAdmin: Boolean,
  arrayAttributeClasses: Array,
});

const emit = defineEmits([
  'save',
  'close',
  'clone',
  'delete',
  'getprev',
  'getnext',
  'update:modelValue',
  'bookmark:modelValue',
]);
const getNested = (path) =>
  computed({
    get() {
      if (typeof path !== 'string') return '';
      return (
        path.split('.').reduce((obj, key) => obj?.[key], props.modelValue) ?? ''
      );
    },
    set(value) {
      if (typeof path !== 'string') return;
      const keys = path.split('.');
      const last = keys.pop();
      let target = props.modelValue;
      for (const key of keys) {
        if (!target[key] || typeof target[key] !== 'object') {
          target[key] = {};
        }
        target = target[key];
      }
      target[last] = value;
      emit('update:modelValue', { ...props.modelValue });
    },
  });
const scrollToSection = (section) => {
  const el = document.getElementById(
    section.title.toLowerCase().replace(/\s+/g, '-')
    //section.title
  );
  if (el) el.scrollIntoView({ behavior: 'smooth' });
};
</script>

<template>
  <div class="w-full">
    <!-- Header -->
    <div class="flex justify-between items-center text-section p-2 mb-0">
      <div class="flex gap-4 items-center">
        <Popper content="Cancel edit" placement="top" hover>
          <UIButton icon="X" @click="emit('close')" class="button" />
        </Popper>

        <h2 class="font-medium">{{ title }}</h2>
      </div>
      <div class="flex gap-4 items-center">
        <Popper content="Scroll to previous" placement="top" hover>
          <UIButton
            :disabled="editIndexInfo.prevItem == null"
            icon="ChevronLeft"
            @click="emit('getprev', editIndexInfo.prevItem)"
            class="button"
          />
        </Popper>
        <div class="min-w-[200px] text-center truncate">
          <h2 class="text-lg">
            {{ modelValue?.name || 'Unnamed Item' }}
          </h2>
        </div>
        <Popper content="Scroll to next" placement="top" hover>
          <UIButton
            :disabled="editIndexInfo.nextItem == null"
            icon="ChevronRight"
            @click="emit('getnext', editIndexInfo.nextItem)"
            class="button"
          />
        </Popper>
      </div>
      <div class="flex gap-4">
        <Popper content="Make a copy" placement="top" hover>
          <UIButton
            v-if="!isCreating && modelValue?._id"
            icon="Copy"
            @click="emit('clone')"
            :disabled="!hasBasic"
            class="button"
          />
        </Popper>
        <Popper content="Bookmark this item" placement="top" hover>
          <UIButton
            v-if="!isCreating && modelValue?._id"
            icon="Star"
            @click="$emit('bookmark', modelValue)"
            :disabled="!hasBasic"
            class="button"
          />
        </Popper>
        <Popper content="Save edit" placement="top" hover>
          <UIButton
            icon="Check"
            @click="emit('save')"
            :disabled="!hasBasic"
            class="button"
          />
        </Popper>

        <Popper content="Cancel edit" placement="top" hover>
          <UIButton icon="X" @click="emit('close')" class="button" />
        </Popper>
      </div>
    </div>

    <!-- Anchor for scroll-to-top -->
    <div id="top" />

    <!-- Section Navigation -->
    <div class="flex flex-wrap gap-4 mb-0 panel-light p-2">
      <button
        v-for="(section, i) in itemEditLayout"
        :key="i"
        @click="scrollToSection(section)"
        class="tab hover:tab-active"
      >
        <span class="text-info">{{ section.title }}</span>
      </button>
    </div>

    <!-- Editable Sections -->
    <div
      v-for="(section, sIdx) in itemEditLayout"
      :key="sIdx"
      :id="section.title.toLowerCase().replace(/\s+/g, '-')"
      class="scroll-mt-24 panel-light pb-4"
    >
      <h3 class="flex gap-4 items-center mb-2 pt-4">
        <UIButton
          icon="ChevronUp"
          as="a"
          href="#top"
          aria-label="Back to top"
          class="button"
        />
        <span class="text-info font-semibold">{{ section.title }}</span>
      </h3>
      <div class="line mr-8 ml-8 mt-2 mb-4"></div>
      <div class="space-y-4 panel-lighter mr-8 ml-8 rounded-md">
        <div
          v-for="(row, rIdx) in section.rows"
          :key="rIdx"
          :class="['grid', section.grid]"
        >
          <div
            v-for="(col, cIdx) in row"
            :key="`${sIdx}-${rIdx}-${cIdx}`"
            :class="col.class"
          >
            <!-- Spacer for layout -->
            <div v-if="col.blank" :class="col.class" aria-hidden="true">
              &nbsp;
            </div>

            <!-- Custom field -->
            <CrudEditDocumentRenderCustom
              v-else-if="
                fields.find((f) => f.key === col.key)?.type === 'custom'
              "
              :field="fields.find((f) => f.key === col.key)"
              :model-value="getNested(col.key).value"
              @update:model-value="(val) => (getNested(col.key).value = val)"
            />

            <!-- Default field -->
            <CrudEditDocumentRender
              v-else-if="col.key"
              :field="fields.find((f) => f.key === col.key) || col"
              :model-value="getNested(col.key).value"
              :has-admin="hasAdmin"
              :array-attribute-classes="arrayAttributeClasses"
              @update:model-value="(val) => (getNested(col.key).value = val)"
            />

            <!-- Unknown or missing field -->
            <div v-else class="text-sm">Invalid field</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Footer Buttons -->
    <div class="flex justify-end items-center w-full text-section p-2">
      <div class="flex gap-4">
        <Popper content="Make a copy" placement="top" hover>
          <UIButton
            v-if="!isCreating && modelValue?._id"
            icon="Copy"
            @click="emit('clone')"
            :disabled="!hasBasic"
            class="button"
          />
        </Popper>
        <Popper content="Bookmark this item" placement="top" hover>
          <UIButton
            v-if="!isCreating && modelValue?._id"
            icon="Star"
            @click="$emit('bookmark', modelValue)"
            :disabled="!hasBasic"
            class="button"
          />
        </Popper>
        <Popper content="Save edit" placement="top" hover>
          <UIButton
            icon="Check"
            @click="emit('save')"
            :disabled="!hasBasic"
            class="button"
          />
        </Popper>

        <Popper content="Cancel edit" placement="top" hover>
          <UIButton icon="X" @click="emit('close')" class="button" />
        </Popper>
      </div>
    </div>
  </div>
</template>

<style scoped>
:deep(html) {
  scroll-behavior: smooth;
}
</style>
