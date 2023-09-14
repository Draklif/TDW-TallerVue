<script setup lang="ts">
  import DeleteIcon from '../components/icons/IconDelete.vue'
  
  // Inicialización de variables
  const { items } = defineProps(['items'])
  const emit = defineEmits(['item-deleted'])

  // Se elimina el primer elemento de la vista
  items.shift()

</script>

<template>
  <table class="table" v-if="items.length > 0">
    <thead>
      <tr>
        <th>#</th>
        <th>Artículo</th>
        <th>Cantidad</th>
        <th>Valor Unitario</th>
        <th>Total</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in items" :key="index">
        <td>{{ index + 1 }}</td>
        <td>{{ item.article }}</td>
        <td>{{ item.quantity }}</td>
        <td>${{ item.value }}</td>
        <td>${{ item.quantity * item.value }}</td>
        <td><button type="button" @click="emit('item-deleted', index)" class="delete-button"><DeleteIcon /></button></td>
      </tr>
    </tbody>
  </table>
  <p v-else>¡Agrega un producto para iniciar!</p>
</template>

<style scoped>
p {
  color: rgb(130, 130, 130);
  font-size: medium;
}

.table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  overflow-y: auto;
  max-height: 320px;
}

.table th,
.table td {
  padding: 10px;
  text-align: center;
  border: 1px solid #ccc;
}

.table th {
  background-color: #f0f0f0;
  font-weight: bold;
}

.delete-button {
  background-color: #ff4d4d;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 6px 10px;
  cursor: pointer;
}

.delete-button:hover {
  background-color: #ff0000;
}
</style>
