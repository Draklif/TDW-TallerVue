<script setup lang="ts">
  import { defineProps, defineEmits } from 'vue'
  import DeleteIcon from '../components/icons/IconDelete.vue'
  
  const { items } = defineProps(['items'])
  const emit = defineEmits(['item-deleted'])
  items.shift()

  const deleteItem = (i:number) => {
    items.splice(i, 1)
    emit('item-deleted')
  }
</script>

<template>
  <table class="table">
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
        <td><button type="button" @click="deleteItem(index)" class="deleteButton"><DeleteIcon /></button></td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
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
