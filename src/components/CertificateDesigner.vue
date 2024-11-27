<script setup lang="ts">
import { ref } from 'vue';
import LeftPanel from './LeftPanel.vue';
import PropertyPanel from './PropertyPanel.vue';
import CertificateCanvas from './CertificateCanvas.vue';
import TextEditorModal from './TextEditorModal.vue';

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

const isLeftPanelOpen = ref(true);
const isPropertyPanelOpen = ref(false);
const isTextEditorOpen = ref(false);
const backgroundImage = ref('');
const fields = ref<Field[]>([]);
const selectedField = ref<Field | null>(null);
const previewField = ref<Field | null>(null);

const certificateData = ref({
  name: '',
  serialCodePrefix: '',
  serialCodeStart: 1
});

const handleUploadBackground = (event: Event) => {
  const input = event.target as HTMLInputElement;
  if (input.files && input.files[0]) {
    const reader = new FileReader();
    reader.onload = (e) => {
      if (e.target?.result) {
        backgroundImage.value = e.target.result as string;
      }
    };
    reader.readAsDataURL(input.files[0]);
  }
};

const handleSelectTemplate = (templateUrl: string) => {
  backgroundImage.value = templateUrl;
};

const addField = (type: string) => {
  const newField: Field = {
    id: Date.now().toString(),
    type,
    value: type === 'text' ? 'Yeni Alan' : `{${type}}`,
    x: 100,
    y: 100,
    width: 200,
    height: 40,
    fontSize: 16,
    fontFamily: 'Arial',
    fontWeight: 400,
    color: '#000000',
    textAlign: 'left',
    letterSpacing: 0,
    lineHeight: 1.4
  };
  fields.value.push(newField);
  selectedField.value = newField;
  isPropertyPanelOpen.value = true;
};

const updateField = (updatedField: Partial<Field>) => {
  if (!selectedField.value) return;
  
  const index = fields.value.findIndex(f => f.id === selectedField.value!.id);
  if (index !== -1) {
    fields.value[index] = { ...fields.value[index], ...updatedField };
    selectedField.value = fields.value[index];
    previewField.value = null; // Reset preview after update
  }
};

const handlePreview = (updates: Partial<Field>) => {
  if (!selectedField.value) return;
  previewField.value = { ...selectedField.value, ...updates };
};

const deleteField = (id: string) => {
  fields.value = fields.value.filter(f => f.id !== id);
  if (selectedField.value?.id === id) {
    selectedField.value = null;
    isPropertyPanelOpen.value = false;
  }
};

const handleSave = () => {
  console.log('Saving certificate...', {
    certificateData: certificateData.value,
    backgroundImage: backgroundImage.value,
    fields: fields.value
  });
};
</script>

<template>
  <div class="d-flex flex-column vh-100">
    <!-- Üst Panel (Özellikler) -->
    <PropertyPanel
      v-if="selectedField"
      :field="selectedField"
      :is-open="isPropertyPanelOpen"
      @update="updateField"
      @delete="deleteField"
      @open-text-editor="isTextEditorOpen = true"
    />

    <div class="d-flex flex-grow-1" style="margin-top: 48px;">
      <!-- Sol Panel -->
      <LeftPanel
        :is-open="isLeftPanelOpen"
        :certificate-data="certificateData"
        @update:certificate-data="certificateData = $event"
        @add-field="addField"
        @upload-background="handleUploadBackground"
        @select-template="handleSelectTemplate"
        @save="handleSave"
      />

      <!-- Ana Canvas -->
      <CertificateCanvas
        :background-image="backgroundImage"
        :fields="fields"
        :selected-field="selectedField"
        :preview-field="previewField"
        @select-field="field => { selectedField = field; isPropertyPanelOpen = true; }"
        @update-field="updateField"
      />
    </div>

    <!-- Metin Düzenleme Modalı -->
    <TextEditorModal
      v-if="selectedField"
      :show="isTextEditorOpen"
      :field="selectedField"
      @update="updateField"
      @preview="handlePreview"
      @close="isTextEditorOpen = false"
    />
  </div>
</template>

<style scoped>
.vh-100 {
  height: 100vh;
}
</style>