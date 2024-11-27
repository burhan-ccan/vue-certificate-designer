<script setup lang="ts">
import { defineProps, defineOptions } from 'vue';

defineOptions({
  name: 'ToolButton'
});

defineProps<{
  variant?: 'primary' | 'secondary' | 'success' | 'warning';
  icon?: any;
}>();
</script>

<template>
  <button
    class="tool-button btn w-100 mb-2 d-flex align-items-center justify-content-center"
    :class="{
      'btn-primary': variant === 'primary',
      'btn-secondary': variant === 'secondary',
      'btn-success': variant === 'success',
      'btn-warning': variant === 'warning'
    }"
  >
    <component
      v-if="icon"
      :is="icon"
      class="me-2"
      style="width: 1.25rem; height: 1.25rem;"
    />
    <slot />
  </button>
</template>