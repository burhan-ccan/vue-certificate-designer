<script setup lang="ts">
import { ref } from 'vue';

interface Field {
  id: string;
  type: string;
  value: string;
  x: number;
  y: number;
  width: number;
  height: number;
  fontSize: number;
  fontFamily: string;
  fontWeight: number;
  color: string;
  textAlign: 'left' | 'center' | 'right';
  fontStyle?: string;
  textDecoration?: string;
  letterSpacing?: number;
  lineHeight?: number;
}

const props = defineProps<{
  field: Field;
  selected: boolean;
  containerWidth: number;
  containerHeight: number;
}>();

const emit = defineEmits<{
  (e: 'select', field: Field): void;
  (e: 'update', field: Field): void;
}>();

const elementRef = ref<HTMLElement | null>(null);
let isDragging = false;
let startX = 0;
let startY = 0;
let originalX = 0;
let originalY = 0;

const onMouseDown = (e: MouseEvent) => {
  if (!elementRef.value) return;
  
  isDragging = true;
  startX = e.clientX;
  startY = e.clientY;
  originalX = props.field.x;
  originalY = props.field.y;
  
  emit('select', props.field);
  
  document.addEventListener('mousemove', onMouseMove);
  document.addEventListener('mouseup', onMouseUp);
};

const onMouseMove = (e: MouseEvent) => {
  if (!isDragging || !elementRef.value) return;
  
  const dx = e.clientX - startX;
  const dy = e.clientY - startY;
  
  const newX = Math.max(0, Math.min(originalX + dx, props.containerWidth - props.field.width));
  const newY = Math.max(0, Math.min(originalY + dy, props.containerHeight - props.field.height));

  emit('update', {
    ...props.field,
    x: newX,
    y: newY
  });
};

const onMouseUp = () => {
  isDragging = false;
  document.removeEventListener('mousemove', onMouseMove);
  document.removeEventListener('mouseup', onMouseUp);
};
</script>

<template>
  <div
    ref="elementRef"
    class="certificate-field"
    :class="{ 'selected': selected }"
    :style="{
      left: `${field.x}px`,
      top: `${field.y}px`,
      width: `${field.width}px`,
      height: `${field.height}px`,
      fontSize: `${field.fontSize}px`,
      fontFamily: field.fontFamily,
      fontWeight: field.fontWeight,
      fontStyle: field.fontStyle,
      textDecoration: field.textDecoration,
      color: field.color,
      textAlign: field.textAlign,
      letterSpacing: `${field.letterSpacing || 0}px`,
      lineHeight: field.lineHeight || 1.4,
      display: 'flex',
      alignItems: 'center',
      justifyContent: field.textAlign === 'center' ? 'center' : 
                     field.textAlign === 'right' ? 'flex-end' : 'flex-start'
    }"
    @mousedown="onMouseDown"
  >
    {{ field.value }}
  </div>
</template>

<style scoped>
.certificate-field {
  position: absolute;
  padding: 0.5rem;
  user-select: none;
  cursor: move;
  border: 1px solid transparent;
}

.certificate-field.selected {
  border: 1px solid #0d6efd;
}
</style>