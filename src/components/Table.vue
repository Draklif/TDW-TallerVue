<script setup lang="ts">
  import { ref, defineProps, defineEmits } from 'vue'
  import DeleteIcon from '../components/icons/IconDelete.vue'
  
  const { items } = defineProps(['items'])
  const emit = defineEmits(['item-deleted'])
  items.shift()

  const deleteItem = (i:number) => {
    items.splice(i, 1)
    emit('item-deleted', items)
  }
</script>

<template>
  <table>
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
        <td><button type="button" @click="deleteItem(index)"><DeleteIcon /></button></td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
</style>
