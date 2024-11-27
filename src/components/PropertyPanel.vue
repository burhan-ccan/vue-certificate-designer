<script setup lang="ts">
import { ref } from 'vue';
import { 
  MinusIcon,
  PlusIcon,
  Bars2Icon,
  Bars3BottomLeftIcon,
  Bars3BottomRightIcon,
  PencilSquareIcon
} from '@heroicons/vue/24/outline';

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
  letterSpacing: number;
  lineHeight: number;
}

const props = defineProps<{
  field: Field;
  isOpen: boolean;
}>();

const emit = defineEmits<{
  (e: 'update', field: Field): void;
  (e: 'delete', id: string): void;
  (e: 'openTextEditor'): void;
}>();

const fontFamilies = [
  'Open Sans',
  'Arial',
  'Times New Roman',
  'Helvetica',
  'Verdana'
];

const alignmentOptions = [
  { value: 'left', icon: Bars3BottomLeftIcon, label: 'Sola Hizala' },
  { value: 'center', icon: Bars2Icon, label: 'Ortala' },
  { value: 'right', icon: Bars3BottomRightIcon, label: 'Sağa Hizala' }
];

const updateField = (updates: Partial<Field>) => {
  emit('update', { ...props.field, ...updates });
};

const decreaseFontSize = () => {
  if (props.field.fontSize > 8) {
    updateField({ fontSize: props.field.fontSize - 1 });
  }
};

const increaseFontSize = () => {
  if (props.field.fontSize < 72) {
    updateField({ fontSize: props.field.fontSize + 1 });
  }
};
</script>

<template>
  <div class="toolbar bg-white border-bottom shadow-sm" :class="{ 'closed': !isOpen }">
    <div class="d-flex align-items-center justify-content-center gap-3 p-2">
      <!-- Font Ailesi -->
      <select 
        :value="field.fontFamily"
        @change="e => updateField({ fontFamily: (e.target as HTMLSelectElement).value })"
        class="form-select form-select-sm"
        style="width: 150px;"
      >
        <option v-for="font in fontFamilies" :key="font" :value="font">{{ font }}</option>
      </select>

      <!-- Font Boyutu -->
      <div class="d-flex align-items-center gap-1">
        <button 
          @click="decreaseFontSize"
          class="btn btn-outline-secondary btn-sm p-1"
          :disabled="field.fontSize <= 8"
        >
          <MinusIcon class="w-4 h-4" />
        </button>
        <input 
          :value="field.fontSize"
          @input="e => updateField({ fontSize: Number((e.target as HTMLInputElement).value) })"
          type="number"
          class="form-control form-control-sm text-center"
          style="width: 60px;"
          min="8"
          max="72"
        />
        <button 
          @click="increaseFontSize"
          class="btn btn-outline-secondary btn-sm p-1"
          :disabled="field.fontSize >= 72"
        >
          <PlusIcon class="w-4 h-4" />
        </button>
      </div>

      <!-- Stil Butonları -->
      <div class="btn-group">
        <button 
          class="btn btn-outline-secondary btn-sm"
          :class="{ active: field.fontWeight >= 700 }"
          @click="updateField({ fontWeight: field.fontWeight >= 700 ? 400 : 700 })"
        >
          <strong>B</strong>
        </button>
        <button 
          class="btn btn-outline-secondary btn-sm"
          :class="{ active: field.fontStyle === 'italic' }"
          @click="updateField({ fontStyle: field.fontStyle === 'italic' ? 'normal' : 'italic' })"
        >
          <em>I</em>
        </button>
        <button 
          class="btn btn-outline-secondary btn-sm"
          :class="{ active: field.textDecoration === 'underline' }"
          @click="updateField({ textDecoration: field.textDecoration === 'underline' ? 'none' : 'underline' })"
        >
          <u>U</u>
        </button>
      </div>

      <!-- Hizalama -->
      <div class="btn-group">
        <button
          v-for="option in alignmentOptions"
          :key="option.value"
          type="button"
          class="btn btn-outline-secondary btn-sm"
          :class="{ active: field.textAlign === option.value }"
          @click="updateField({ textAlign: option.value })"
          :title="option.label"
        >
          <component :is="option.icon" class="w-4 h-4" />
        </button>
      </div>

      <!-- Metin Düzenle -->
      <button 
        class="btn btn-outline-primary btn-sm"
        @click="$emit('openTextEditor')"
        title="Metni Düzenle"
      >
        <PencilSquareIcon class="w-4 h-4" />
      </button>

      <!-- Renk Seçici -->
      <input 
        :value="field.color"
        @input="e => updateField({ color: (e.target as HTMLInputElement).value })"
        type="color"
        class="form-control form-control-color p-0"
        style="width: 32px; height: 31px;"
      />
    </div>
  </div>
</template>

<style scoped>
.toolbar {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1030;
  transition: all 0.3s ease;
}

.toolbar.closed {
  transform: translateY(-100%);
}

.w-4 {
  width: 1rem;
  height: 1rem;
}

.h-4 {
  width: 1rem;
  height: 1rem;
}

.form-control-sm {
  height: 31px;
}

.btn-sm {
  height: 31px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.form-select-sm {
  height: 31px;
}
</style>