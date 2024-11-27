<script setup lang="ts">
defineOptions({
  name: 'AppLayout'
});
</script>

<template>
  <div class="min-h-screen bg-gray-50">
    <slot />
  </div>
</template>