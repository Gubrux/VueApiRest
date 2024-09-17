<template>
  <div class="center">
    <PersonaTable />
  </div>
</template>

<script setup>
import PersonaTable from './PersonaTable.vue';
</script>

<style scoped>
.center {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  width: 100%;
  background-color: #f5f5f5;
}
</style>
