<template>
  <q-page class="q-pa-md">
    <q-list bordered padding v-if="!loading && !errorMessage">
      <q-item-label header class="text-h5">Lista de Personas</q-item-label>
      <q-item v-for="persona in personas" :key="persona.id" class="q-mb-sm">
        <q-item-section>
          <q-item-label
            ><strong>Nombre:</strong> {{ persona.nombre }}</q-item-label
          >
          <q-item-label
            ><strong>Apellido:</strong> {{ persona.apellido }}</q-item-label
          >
          <q-item-label
            ><strong>Documento:</strong> {{ persona.documento }}</q-item-label
          >
        </q-item-section>
        <q-item-section side>
          <q-btn
            flat
            round
            icon="delete"
            color="negative"
            @click="deletePersona(persona.id)"
          />
        </q-item-section>
      </q-item>
    </q-list>
    <q-notify
      v-if="errorMessage"
      :message="errorMessage"
      type="negative"
      position="top-right"
    />
    <q-spinner v-if="loading" />
  </q-page>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted } from 'vue';
import { apiUrl } from '../api/apiUrl';
import axios from 'axios';

interface Persona {
  id: number;
  nombre: string;
  apellido: string;
  documento: string;
}

export default defineComponent({
  setup() {
    const personas = ref<Persona[]>([]);
    const loading = ref(false);
    const errorMessage = ref<string | null>(null);

    const fetchPersonas = async () => {
      loading.value = true;
      errorMessage.value = null;
      try {
        const response = await axios.get<Persona[]>(apiUrl);
        personas.value = response.data;
      } catch (error) {
        errorMessage.value = 'Error al obtener las personas';
      } finally {
        loading.value = false;
      }
    };

    const deletePersona = async (id: number) => {
      try {
        await axios.delete(`http://localhost:3001/personas/${id}`);
        fetchPersonas();
      } catch (error) {
        errorMessage.value = 'Error al eliminar la persona';
      }
    };

    onMounted(() => {
      fetchPersonas();
    });

    return {
      personas,
      loading,
      errorMessage,
      deletePersona,
    };
  },
});
</script>

<style scoped>
.q-page {
  max-width: 600px;
  margin: auto;
}

.q-item {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 16px;
}
</style>
