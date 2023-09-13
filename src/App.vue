<script setup lang="ts">
  import { ref, reactive } from 'vue'
  import Table from './components/Table.vue'

  const newArticle = ref('');
  const newQuantity = ref(1);
  const newValue = ref(1);

  const quantityTotal = ref(0);

  const totalBase = ref(0);
  const totalAbsolute = ref(0);
  const discount = ref(0);

  const items = reactive([{}])

  const addItem = () => {
    if (newArticle.value === '') {
      alert('Ingrese un nombre para su artículo')
      return
    }

    if (newQuantity.value <= 0 || newValue.value <= 0) {
      alert('Ingrese únicamente valores positivos diferentes de 0')
      return
    }

    items.push({
      article: newArticle.value,
      quantity: newQuantity.value,
      value: newValue.value
    })

    calculatePrice()

    newArticle.value = ''
    newQuantity.value = 1
    newValue.value = 1
  }

  const onItemDeleted = (items:[{}]) => {
    calculatePrice()
  }

  const calculatePrice = () => {
    quantityTotal.value = quantityTotal.value + newQuantity.value
    totalBase.value = totalBase.value + (newValue.value * newQuantity.value)

    if (totalBase.value >= 60000) {
      discount.value = 5
    } else if (totalBase.value >= 120000) {
      discount.value = 10
    } else if (totalBase.value >= 240000) {
      discount.value = 15
    }

    if (quantityTotal.value >= 6 && discount.value < 10) {
      discount.value = 10
    } else if (quantityTotal.value >= 12 && discount.value < 20) {
      discount.value = 20
    }

    discount.value = quantityTotal.value < 6 && totalBase.value < 60000 ? 0 : discount.value 

    totalAbsolute.value = Math.floor(totalBase.value * (1 - (discount.value / 100)))
  }
</script>

<template>
  <div class="formContainer">
    <form style="display: flex; flex-direction: column;" @submit.prevent="addItem">
      <label for="txtArticle">Artículo</label>
      <input id="txtArticle" type="text" v-model="newArticle">
      <label for="txtQuantity">Cantidad</label>
      <input id="txtQuantity" type="number" v-model="newQuantity">
      <label for="txtValue">Valor Unitario</label>
      <input id="txtValue" type="number" v-model="newValue">
      <button type="submit">Agregar</button>
    </form>
  </div>

  <div class="dataContainer">
    <Table :items="items" @item-deleted="onItemDeleted"/>

    <div class="showTotal">
      <label for="totalBase">Total compra: </label>
      <p id="totalBase" style="display: inline;">${{ totalBase }}</p>
      <br>
      <label for="discount">Descuento: </label>
      <p id="discount" style="display: inline;">{{ discount }}%</p>
      <br>
      <label for="total">Total a pagar: </label>
      <p id="total" style="display: inline;">${{ totalAbsolute }}</p>
    </div>
  </div>
</template>

<style scoped>
template {
  display: flex;
  flex-direction: row;
}

.formContainer {
  display: flex;
  justify-content: center;
  color: white;
  border-radius: 16px;
  background-color: rgb(44, 44, 44);
  padding: 16px;
  margin: 16px;
}

button {
  width: 256px;
  margin-top: 16px;
  border: 0;
  border-radius: 16px;
}

.dataContainer {
  display: flex;
  flex-direction: column;
  margin: 16px;
}

.showTotal {  
  margin-top: 24px;
  align-self: end;
}

</style>
