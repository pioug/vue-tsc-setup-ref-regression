<script setup lang="ts">
import { onMounted, ref } from 'vue';

const status = ref<'uploading' | 'uploaded' | 'error'>('uploading');
status.value = 'uploading';

onMounted(async () => {
  await Promise.resolve();
  status.value = 'error';
});
</script>

<template>
  <span v-if="status === 'error'">Upload failed</span>
</template>
