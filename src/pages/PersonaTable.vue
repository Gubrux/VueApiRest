<template>
  <div class="q-pa-md">
    <div class="row q-gutter-md items-center">
      <div class="col">
        <CrearPersonaModal @crear-persona="fetchPersonas" />
      </div>
      <div class="col">
        <InputBuscar @buscar="filtrarFullname"  />
      </div>
      <div class="col">
        <InputBuscar @buscar="filtrarPorDocumento" />
      </div>
      <div class="col-auto">
        <q-btn
          color="primary"
          icon="edit"
          :disable="selected.length === 0"
          @click="editPersona(selected[0])"
          class="q-mr-sm q-btn--outline q-mr-md"
        />
        <q-btn
          color="negative"
          icon="delete"
          :disable="selected.length === 0"
          @click="removePersona(selected[0].id)"
          class="q-btn--outline"
        />
      </div>
    </div>
    <q-table
      title="Personas"
      :rows="personasFiltradas"
      :columns="columns"
      row-key="id"
      selection="single"
      v-model:selected="selected"
    />
    <q-dialog v-model="dialogOpen">
      <q-card>
        <q-card-section>
          <div class="text-h6">Editar Persona</div>
        </q-card-section>
        <q-card-section>
          <q-input v-model="editForm.nombre" label="Nombre" />
          <q-input v-model="editForm.apellido" label="Apellido" />
          <q-input v-model="editForm.documento" label="Documento" />
        </q-card-section>
        <q-card-actions align="right">
          <q-btn flat label="Cancelar" v-close-popup />
          <q-btn flat label="Guardar" @click="updatePersona" />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </div>
</template>

<script setup lang="ts">
import axios from 'axios';
import { apiUrl } from 'src/api/apiUrl';
import { onMounted, ref } from 'vue';
import CrearPersonaModal from './CrearPersonaModal.vue';
import InputBuscar from './InputBuscar.vue';

const columns = [
  {
    name: 'nombre',
    required: true,
    label: 'Nombre',
    field: 'nombre',
    sortable: true,
  },
  {
    name: 'apellido',
    label: 'Apellido',
    field: 'apellido',
    sortable: true,
  },
  {
    name: 'documento',
    label: 'Documento',
    field: 'documento',
    sortable: true,
  },
];

interface Persona {
  id: number;
  nombre: string;
  apellido: string;
  documento: string;
}

const todasPersonas = ref<Persona[]>([]);
const personasFiltradas = ref<Persona[]>([]);
const loading = ref(false);
const errorMessage = ref<string | null>(null);
const dialogOpen = ref(false);
const editForm = ref<Persona>({
  id: 0,
  nombre: '',
  apellido: '',
  documento: '',
});

const selected = ref<Persona[]>([]);

const filtrosInputs: { fullname?: string; documento?: string } = {};

const filtrarFullname = (fullname: string) => {
  console.log('buscando por nombre completo', fullname);
  filtrosInputs.fullname = fullname;
  fetchPersonas();
};

const filtrarPorDocumento = (documento: string) => {
  console.log('buscando por documento:', documento);
  filtrosInputs.documento = documento;
  fetchPersonas();
};

const fetchPersonas = async () => {
  loading.value = true;
  errorMessage.value = null;
  try {
    const response = await axios.get<Persona[]>(apiUrl, {
      params: filtrosInputs,
    });
    todasPersonas.value = response.data;
    personasFiltradas.value = response.data;
  } catch (error) {
    errorMessage.value = 'Error al obtener las personas';
  } finally {
    loading.value = false;
  }
};

const deletePersona = async (id: number) => {
  try {
    await axios.delete(`${apiUrl}/${id}`);
    fetchPersonas();
  } catch (error) {
    errorMessage.value = 'Error al eliminar la persona';
  }
};

const removePersona = (id: number) => {
  if (confirm('¿Estás seguro de que deseas eliminar esta persona?')) {
    deletePersona(id);
  }
};

const editPersona = (persona: Persona) => {
  editForm.value = { ...persona };
  dialogOpen.value = true;
};

const updatePersona = async () => {
  try {
    await axios.put(`${apiUrl}/${editForm.value.id}`, editForm.value);
    fetchPersonas();
    dialogOpen.value = false;
  } catch (error) {
    errorMessage.value = 'Error al actualizar la persona';
  }
};

onMounted(() => {
  fetchPersonas();
});
</script>
