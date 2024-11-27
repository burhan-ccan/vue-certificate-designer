<script setup lang="ts">
import { ref } from 'vue';
import {
  DocumentPlusIcon,
  UserIcon,
  IdentificationIcon,
  AcademicCapIcon,
  PhotoIcon,
  Bars3Icon,
  ArrowDownTrayIcon,
  ChevronDownIcon,
  BuildingLibraryIcon,
  IdentificationIcon as StudentNumberIcon
} from '@heroicons/vue/24/outline';

interface CertificateData {
  name: string;
  serialCodePrefix: string;
  serialCodeStart: number;
}

defineProps<{
  isOpen: boolean;
  certificateData: CertificateData;
}>();

const emit = defineEmits<{
  (e: 'update:certificateData', value: CertificateData): void;
  (e: 'addField', type: string): void;
  (e: 'uploadBackground', event: Event): void;
  (e: 'selectTemplate', template: string): void;
  (e: 'save'): void;
}>();

const isTemplatesOpen = ref(false);

const templates = [
  { id: 'certificate1', name: 'Klasik Sertifika', thumbnail: '#' },
  { id: 'certificate2', name: 'Modern Sertifika', thumbnail: '#' },
  { id: 'certificate3', name: 'Profesyonel Sertifika', thumbnail: '#' }
];
</script>

<template>
  <div 
    class="panel-container"
    :class="{ 'w-300px': isOpen, 'w-0': !isOpen }"
  >
    <div class="panel-header">
      <h2 class="h5 mb-0 d-flex align-items-center">
        <Bars3Icon class="me-2" style="width: 20px; height: 20px;" />
        Sertifika Tasarımı
      </h2>
    </div>

    <div class="panel-content">
      <!-- Sertifika Bilgileri -->
      <div class="mb-4">
        <h3 class="h6 mb-3">Sertifika Bilgileri</h3>
        <div class="mb-3">
          <label class="form-label">Sertifika Adı</label>
          <input
            v-model="certificateData.name"
            type="text"
            class="form-control"
            placeholder="Sertifika adını giriniz"
          />
        </div>
        <div class="mb-3">
          <label class="form-label">Seri Kod Sabit Değeri</label>
          <input
            v-model="certificateData.serialCodePrefix"
            type="text"
            class="form-control"
            placeholder="Örn: CERT-"
          />
        </div>
        <div class="mb-3">
          <label class="form-label">Seri Kod Başlangıç Değeri</label>
          <input
            v-model="certificateData.serialCodeStart"
            type="number"
            class="form-control"
            min="1"
          />
        </div>
      </div>

      <!-- Arka Plan -->
      <div class="mb-4">
        <h3 class="h6 mb-3">Arka Plan</h3>
        <div class="position-relative mb-3">
          <input
            type="file"
            @change="$emit('uploadBackground', $event)"
            accept="image/*"
            class="position-absolute opacity-0 w-100 h-100 cursor-pointer"
            style="z-index: 2;"
          />
          <div class="bg-light rounded p-4 text-center">
            <PhotoIcon class="mb-2" style="width: 24px; height: 24px;" />
            <p class="mb-0 small">Sürükle bırak veya seç</p>
          </div>
        </div>

        <!-- Hazır Şablonlar -->
        <div class="border rounded">
          <button
            class="btn btn-light w-100 d-flex align-items-center justify-content-between p-2"
            @click="isTemplatesOpen = !isTemplatesOpen"
          >
            <span class="small">Hazır Şablonlar</span>
            <ChevronDownIcon 
              class="transition-transform"
              :class="{ 'rotate-180': isTemplatesOpen }"
              style="width: 16px; height: 16px;"
            />
          </button>
          <div v-if="isTemplatesOpen" class="p-2">
            <div 
              v-for="template in templates" 
              :key="template.id"
              class="border rounded p-2 mb-2 cursor-pointer hover-shadow transition-all"
              @click="$emit('selectTemplate', template.id)"
            >
              <img 
                :src="template.thumbnail" 
                :alt="template.name"
                class="w-100 rounded mb-1"
                style="height: 60px; object-fit: cover;"
              />
              <p class="small mb-0 text-center">{{ template.name }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Alan Ekleme -->
      <div class="mb-4">
        <h3 class="h6 mb-3">Alan Ekle</h3>
        <div class="row g-2">
          <div class="col-6">
            <button 
              @click="$emit('addField', 'text')"
              class="btn btn-outline-primary w-100 h-100 d-flex flex-column align-items-center justify-content-center p-3"
            >
              <DocumentPlusIcon style="width: 24px; height: 24px;" />
              <span class="small mt-2">Metin</span>
            </button>
          </div>
          <div class="col-6">
            <button 
              @click="$emit('addField', 'studentName')"
              class="btn btn-outline-success w-100 h-100 d-flex flex-column align-items-center justify-content-center p-3"
            >
              <UserIcon style="width: 24px; height: 24px;" />
              <span class="small mt-2">Öğrenci</span>
            </button>
          </div>
          <div class="col-6">
            <button 
              @click="$emit('addField', 'identityNo')"
              class="btn btn-outline-info w-100 h-100 d-flex flex-column align-items-center justify-content-center p-3"
            >
              <IdentificationIcon style="width: 24px; height: 24px;" />
              <span class="small mt-2">Kimlik No</span>
            </button>
          </div>
          <div class="col-6">
            <button 
              @click="$emit('addField', 'courseName')"
              class="btn btn-outline-warning w-100 h-100 d-flex flex-column align-items-center justify-content-center p-3"
            >
              <AcademicCapIcon style="width: 24px; height: 24px;" />
              <span class="small mt-2">Ders</span>
            </button>
          </div>
          <div class="col-6">
            <button 
              @click="$emit('addField', 'schoolName')"
              class="btn btn-outline-secondary w-100 h-100 d-flex flex-column align-items-center justify-content-center p-3"
            >
              <BuildingLibraryIcon style="width: 24px; height: 24px;" />
              <span class="small mt-2">Okul Adı</span>
            </button>
          </div>
          <div class="col-6">
            <button 
              @click="$emit('addField', 'studentNumber')"
              class="btn btn-outline-dark w-100 h-100 d-flex flex-column align-items-center justify-content-center p-3"
            >
              <StudentNumberIcon style="width: 24px; height: 24px;" />
              <span class="small mt-2">Öğrenci No</span>
            </button>
          </div>
        </div>
      </div>

      <!-- Kaydetme -->
      <div class="panel-footer">
        <button 
          @click="$emit('save')"
          class="btn btn-primary w-100 d-flex align-items-center justify-content-center"
        >
          <ArrowDownTrayIcon class="me-2" style="width: 20px; height: 20px;" />
          Kaydet
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.panel-container {
  background: white;
  box-shadow: var(--bs-box-shadow-lg);
  transition: width 0.3s ease;
  height: calc(100vh - 48px);
  position: relative;
  z-index: 1000;
  display: flex;
  flex-direction: column;
}

.panel-header {
  padding: 1rem;
  border-bottom: 1px solid #dee2e6;
  background: white;
}

.panel-content {
  padding: 1rem;
  overflow-y: auto;
  flex-grow: 1;
  height: 100%;
}

.panel-footer {
  padding: 1rem;
  background: white;
  border-top: 1px solid #dee2e6;
  position: sticky;
  bottom: 0;
}

.w-300px {
  width: 300px;
}

.w-0 {
  width: 0;
  overflow: hidden;
}

.hover-shadow:hover {
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

.transition-transform {
  transition: transform 0.3s ease;
}

.rotate-180 {
  transform: rotate(180deg);
}

.cursor-pointer {
  cursor: pointer;
}
</style>