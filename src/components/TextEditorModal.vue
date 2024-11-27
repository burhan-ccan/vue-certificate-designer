<script setup lang="ts">
import { ref, watch } from 'vue';
import { XMarkIcon } from '@heroicons/vue/24/outline';

interface Field {
  id: string;
  type: string;
  value: string;
  letterSpacing: number;
  lineHeight: number;
  width: number;
  height: number;
  x: number;
  y: number;
}

const props = defineProps<{
  show: boolean;
  field: Field;
}>();

const emit = defineEmits<{
  (e: 'update', field: Partial<Field>): void;
  (e: 'preview', field: Partial<Field>): void;
  (e: 'close'): void;
}>();

const localValue = ref(props.field.value);
const localLetterSpacing = ref(props.field.letterSpacing || 0);
const localLineHeight = ref(props.field.lineHeight || 1.4);
const localWidth = ref(props.field.width);
const localHeight = ref(props.field.height);
const localX = ref(props.field.x);
const localY = ref(props.field.y);

// Watch for field changes (including position updates from dragging)
watch(() => props.field, (newField) => {
  localX.value = newField.x;
  localY.value = newField.y;
  localWidth.value = newField.width;
  localHeight.value = newField.height;
  localValue.value = newField.value;
  localLetterSpacing.value = newField.letterSpacing || 0;
  localLineHeight.value = newField.lineHeight || 1.4;
}, { deep: true });

// Reset values when modal opens
watch(() => props.show, (newVal) => {
  if (newVal) {
    localValue.value = props.field.value;
    localLetterSpacing.value = props.field.letterSpacing || 0;
    localLineHeight.value = props.field.lineHeight || 1.4;
    localWidth.value = props.field.width;
    localHeight.value = props.field.height;
    localX.value = props.field.x;
    localY.value = props.field.y;
  }
});

const updatePreview = () => {
  emit('update', {
    value: localValue.value,
    letterSpacing: localLetterSpacing.value,
    lineHeight: localLineHeight.value,
    width: localWidth.value,
    height: localHeight.value,
    x: localX.value,
    y: localY.value
  });
};

// Watch for local changes and update preview
watch(
  [localValue, localLetterSpacing, localLineHeight, localWidth, localHeight, localX, localY], 
  () => {
    updatePreview();
  }
);
</script>

<template>
  <div v-if="show" class="text-editor-modal" @click.self="$emit('close')">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Metni Düzenle</h5>
        <button class="btn-close" @click="$emit('close')">
          <XMarkIcon class="w-4 h-4" />
        </button>
      </div>
      <div class="modal-body">
        <!-- Metin Alanı -->
        <div class="mb-3">
          <label class="form-label">Metin</label>
          <textarea
            v-if="field.type === 'text'"
            v-model="localValue"
            class="form-control"
            rows="4"
            placeholder="Metninizi girin..."
          ></textarea>
          <input
            v-else
            :value="field.value"
            type="text"
            class="form-control"
            disabled
          />
        </div>

        <!-- Boyutlar -->
        <div class="mb-3">
          <label class="form-label">Boyutlar</label>
          <div class="row g-2">
            <div class="col-6">
              <div class="input-group input-group-sm">
                <span class="input-group-text">Genişlik</span>
                <input
                  v-model="localWidth"
                  type="number"
                  class="form-control"
                  min="50"
                />
              </div>
            </div>
            <div class="col-6">
              <div class="input-group input-group-sm">
                <span class="input-group-text">Yükseklik</span>
                <input
                  v-model="localHeight"
                  type="number"
                  class="form-control"
                  min="20"
                />
              </div>
            </div>
          </div>
        </div>

        <!-- Harf Aralığı -->
        <div class="mb-3">
          <label class="form-label">Harf aralığı</label>
          <div class="d-flex align-items-center gap-2">
            <input 
              v-model="localLetterSpacing"
              type="range" 
              class="form-range flex-grow-1"
              min="-2" 
              max="10" 
              step="0.1"
            />
            <input 
              v-model="localLetterSpacing"
              type="number" 
              class="form-control"
              style="width: 70px;"
              step="0.1"
            />
          </div>
        </div>

        <!-- Satır Aralığı -->
        <div class="mb-3">
          <label class="form-label">Satır aralığı</label>
          <div class="d-flex align-items-center gap-2">
            <input 
              v-model="localLineHeight"
              type="range" 
              class="form-range flex-grow-1"
              min="0.5" 
              max="3" 
              step="0.1"
            />
            <input 
              v-model="localLineHeight"
              type="number" 
              class="form-control"
              style="width: 70px;"
              step="0.1"
            />
          </div>
        </div>

        <!-- Pozisyon -->
        <div class="mb-3">
          <label class="form-label">Pozisyon</label>
          <div class="row g-2">
            <div class="col-6">
              <div class="input-group input-group-sm">
                <span class="input-group-text">X</span>
                <input
                  v-model="localX"
                  type="number"
                  class="form-control"
                  min="0"
                />
              </div>
            </div>
            <div class="col-6">
              <div class="input-group input-group-sm">
                <span class="input-group-text">Y</span>
                <input
                  v-model="localY"
                  type="number"
                  class="form-control"
                  min="0"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.text-editor-modal {
  position: fixed;
  top: 48px;
  right: 0;
  bottom: 0;
  width: 400px;
  background: rgba(0, 0, 0, 0.1);
  z-index: 1040;
  display: flex;
  align-items: flex-start;
  justify-content: flex-end;
}

.modal-content {
  background: white;
  height: 100%;
  width: 100%;
  box-shadow: -2px 0 5px rgba(0, 0, 0, 0.1);
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem;
  border-bottom: 1px solid #dee2e6;
}

.modal-title {
  margin: 0;
  font-size: 1.25rem;
}

.modal-body {
  padding: 1rem;
  overflow-y: auto;
  height: calc(100% - 60px);
}

.btn-close {
  background: none;
  border: none;
  padding: 0.25rem;
  cursor: pointer;
  opacity: 0.5;
  transition: opacity 0.2s;
}

.btn-close:hover {
  opacity: 1;
}

.w-4 {
  width: 1rem;
}

.h-4 {
  height: 1rem;
}
</style>