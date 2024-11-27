<script setup lang="ts">
import { defineProps, defineEmits, ref, onMounted } from 'vue';
import CertificateField from './CertificateField.vue';

interface Field {
  id: string;
  type: string;
  value: string;
  x: number;
  y: number;
  fontSize: number;
  fontFamily: string;
  fontWeight: number;
  color: string;
}

defineProps<{
  backgroundImage: string;
  fields: Field[];
  selectedField: Field | null;
  previewField: Field | null;
}>();

const emit = defineEmits<{
  (e: 'selectField', field: Field): void;
  (e: 'updateField', field: Field): void;
}>();

const containerRef = ref<HTMLElement | null>(null);
const containerWidth = ref(0);
const containerHeight = ref(0);

onMounted(() => {
  if (containerRef.value) {
    containerWidth.value = containerRef.value.offsetWidth;
    containerHeight.value = containerRef.value.offsetHeight;
  }
});
</script>

<template>
  <div class="flex-grow-1 p-4 overflow-auto position-relative">
    <div 
      ref="containerRef"
      class="bg-white mx-auto shadow position-relative"
      style="width: 297mm; height: 210mm;"
    >
      <img 
        v-if="backgroundImage"
        :src="backgroundImage"
        class="position-absolute top-0 start-0 w-100 h-100 object-fit-cover"
        alt="Sertifika Arka Planı"
      />

      <CertificateField
        v-for="field in fields"
        :key="field.id"
        :field="previewField && previewField.id === field.id ? previewField : field"
        :selected="selectedField?.id === field.id"
        :container-width="containerWidth"
        :container-height="containerHeight"
        @select="emit('selectField', $event)"
        @update="emit('updateField', $event)"
      />
    </div>
  </div>
</template>