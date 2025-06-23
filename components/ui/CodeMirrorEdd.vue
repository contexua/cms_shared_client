<script setup>
import { ref, watch, onMounted, onBeforeUnmount, defineExpose } from 'vue';
import { EditorState } from '@codemirror/state';
import { EditorView, keymap, Decoration } from '@codemirror/view';
import { defaultKeymap } from '@codemirror/commands';
import { json } from '@codemirror/lang-json';
import {
  lineNumbers,
  highlightActiveLineGutter,
  highlightActiveLine,
} from '@codemirror/view';
import { bracketMatching } from '@codemirror/language';

const props = defineProps({
  modelValue: String,
  readonlyContent: String,
  extensions: {
    type: Array,
    default: () => [],
  },
});

const emit = defineEmits(['update:modelValue']);
const editorRef = ref(null);
let view;

defineExpose({ getView: () => view });

const createEditor = (doc, extensions, isEditable = true) => {
  const state = EditorState.create({
    doc,
    extensions: [
      keymap.of(defaultKeymap),
      json(),
      lineNumbers(),
      highlightActiveLineGutter(),
      highlightActiveLine(),
      bracketMatching(),
      EditorView.lineWrapping,
      EditorView.editable.of(isEditable),
      ...(isEditable
        ? [
            EditorView.updateListener.of((update) => {
              if (update.docChanged) {
                emit('update:modelValue', update.state.doc.toString());
              }
            }),
          ]
        : []),
      ...extensions,
    ],
  });
  return new EditorView({ state, parent: editorRef.value });
};

const updateEditor = (doc, isEditable) => {
  if (!editorRef.value) return;
  if (view) view.destroy();
  view = createEditor(doc, props.extensions, isEditable);
};

onMounted(() => {
  const content = props.readonlyContent ?? props.modelValue ?? '';
  const isReadonly = props.readonlyContent != null;
  updateEditor(content, !isReadonly);
});

watch(
  () => props.readonlyContent,
  (newContent) => {
    const content = newContent ?? props.modelValue ?? '';
    const isReadonly = newContent != null;
    updateEditor(content, !isReadonly);
  }
);

watch(
  () => props.modelValue,
  (val) => {
    if (props.readonlyContent != null) return;
    if (view && val !== view.state.doc.toString()) {
      view.dispatch({
        changes: { from: 0, to: view.state.doc.length, insert: val },
      });
    }
  }
);

watch(
  () => props.extensions,
  () => {
    const content = view?.state.doc.toString() ?? '';
    const isReadonly = props.readonlyContent != null;
    updateEditor(content, !isReadonly);
  },
  { deep: true }
);

onBeforeUnmount(() => {
  view?.destroy();
});
</script>

<template>
  <div ref="editorRef" class="w-full h-full borderd rounded" />
</template>

<style>
.cm-editor {
  font-family: monospace;
  font-size: 0.85rem;
  height: 100%;
}
</style>
