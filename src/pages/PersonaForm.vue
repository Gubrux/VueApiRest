<template>
  <q-page class="q-pa-md">
    <q-form @submit.prevent="submitForm">
      <q-input v-model="persona.nombre" label="Nombre" />
      <q-input v-model="persona.apellido" label="Apellido" />
      <q-input v-model="persona.documento" label="Documento" />
      <q-btn type="submit" label="Guardar" color="primary" />
    </q-form>
  </q-page>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import axios from 'axios';
import { apiUrl } from 'src/api/apiUrl';

interface Persona {
  id: number;
  nombre: string;
  apellido: string;
  documento: string;
}

const emit = defineEmits(['crear-persona']);

const persona = ref<Persona>({
  id: 0,
  nombre: '',
  apellido: '',
  documento: '',
});

const submitForm = async () => {
  try {
    await axios.post(apiUrl, persona.value);
    emit('crear-persona');
  } catch (error) {
    console.error('Error al guardar la persona:', error);
  }
};
</script>

<style scoped>
.q-page {
  max-width: 600px;
  margin: auto;
}
</style>
