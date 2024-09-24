<template>
  <div>
    <q-input
      v-model="inputModel"
      clearable
      color="blue-grey-4"
      label="Buscar"
    />
  </div>
</template>

<script lang="ts" setup>
import { ref, watch, defineEmits } from 'vue';

const inputModel = ref('');
const emit = defineEmits(['buscar']);

watch(inputModel, (newValue) => {
  console.log('Nuevo valor del input:', newValue);
  emit('buscar', newValue);
});
</script>

<style scoped></style>
