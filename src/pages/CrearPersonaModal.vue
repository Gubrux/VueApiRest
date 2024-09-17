<template>
  <div class="q-pa-md q-gutter-sm">
    <q-btn label="Crear Persona" color="primary" @click="dialog = true" />

    <q-dialog
      v-model="dialog"
      persistent
      :maximized="maximizedToggle"
      transition-show="slide-up"
      transition-hide="slide-down"
    >
      <q-card class="bg-blue-2 text-white">
        <q-bar>
          <q-space />
          <q-btn
            dense
            flat
            icon="minimize"
            @click="maximizedToggle = false"
            :disable="!maximizedToggle"
          >
            <q-tooltip v-if="maximizedToggle" class="bg-white text-primary"
              >Minimize</q-tooltip
            >
          </q-btn>
          <q-btn
            dense
            flat
            icon="crop_square"
            @click="maximizedToggle = true"
            :disable="maximizedToggle"
          >
            <q-tooltip v-if="!maximizedToggle" class="bg-white text-primary"
              >Maximize</q-tooltip
            >
          </q-btn>
          <q-btn dense flat icon="close" v-close-popup>
            <q-tooltip class="bg-white text-primary">Close</q-tooltip>
          </q-btn>
        </q-bar>

        <q-card-section>
          <div class="text-h6">Crear una nueva persona</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-form @submit.prevent="submitForm">
            <q-input v-model="persona.nombre" label="Nombre" />
            <q-input v-model="persona.apellido" label="Apellido" />
            <q-input v-model="persona.documento" label="Documento" />
            <q-btn type="submit" label="Guardar" color="primary" />
          </q-form>
        </q-card-section>
      </q-card>
    </q-dialog>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';
import { apiUrl } from 'src/api/apiUrl';

interface Persona {
  id: number;
  nombre: string;
  apellido: string;
  documento: string;
}

const emit = defineEmits(['crear-persona', 'close-modal']);

const dialog = ref(false);
const maximizedToggle = ref(false);
const initialPersona = {
  id: 0,
  nombre: '',
  apellido: '',
  documento: '',
};
const persona = ref<Persona>({ ...initialPersona });

const closeDialog = () => {
  dialog.value = false;
};
const resetForm = () => {
  persona.value = { ...initialPersona };
  console.log('limpiando!');
};
const submitForm = async () => {
  try {
    await axios.post(apiUrl, persona.value);
    emit('crear-persona');
    closeDialog();
    resetForm();
  } catch (error) {
    console.error('Error al guardar la persona:', error);
  }
};
</script>
