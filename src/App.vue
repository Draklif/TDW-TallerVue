<script setup lang="ts">
import { ref, reactive } from 'vue'
import Swal from 'sweetalert2'
import Table from './components/Table.vue'

// Inicialización de variables
const newArticle = ref('')
const newQuantity = ref(1)
const newValue = ref(1)
const totalBase = ref(0)
const totalAbsolute = ref(0)
const discount = ref(0)

// Se define el tipo Item con las propiedades que tomará siempre
type Item = {
  article: string
  quantity: number
  value: number
}

// Se define un arreglo reactivo que toma únicamente arreglos de tipo Item
const items = reactive<Item[]>([])

/** Método que se ejecuta al momento de agregar un artículo */
const addItem = () => {
  // Establece en mayúsculas el nombre del artículo
  newArticle.value = newArticle.value.toUpperCase()

  //  Validación para el nombre del artículo no vacío
  if (newArticle.value === '') {
    Swal.fire({
      text: 'Por favor ingrese un nombre para su artículo',
      icon: 'error',
      position: 'top',
      toast: true,
      timer: 4000,
      timerProgressBar: true,
      showConfirmButton: false,
      width: 520
    })
    return
  }

  //  Validación para la cantidad y el valor del artículo mayor a 0
  if (newQuantity.value <= 0 || newValue.value <= 0) {
    Swal.fire({
      text: 'Por favor ingrese valores superiores a 0',
      icon: 'error',
      position: 'top',
      toast: true,
      timer: 4000,
      timerProgressBar: true,
      showConfirmButton: false,
      width: 520
    })
    return
  }
  
  // Se evalúa si el artículo ya se encuentra en la lista
  if (items.find((item) => (item.article === newArticle.value))) {
    // En tal caso de que sí, se informa al usuario y se actualiza la cantidad de productos en la lista
    Swal.fire({
      text: 'Este artículo ya existe. Se le han aumentado las cantidades',
      icon: 'info',
      position: 'top',
      toast: true,
      timer: 4000,
      timerProgressBar: true,
      showConfirmButton: false,
      width: 520
    })

    let tempIndex = items.findIndex((item) => (item.article === newArticle.value))
    items[tempIndex].quantity = items[tempIndex].quantity + newQuantity.value 
  } else {
    // De lo contrario, se informa al usuario y se agrega el producto
    Swal.fire({
      text: `Se ha agregado x${newQuantity.value} ${newArticle.value} por $${newValue.value}`,
      icon: 'success',
      position: 'top',
      toast: true,
      timer: 4000,
      timerProgressBar: true,
      showConfirmButton: false,
      width: 520
    })

    items.push({
      article: newArticle.value,
      quantity: newQuantity.value,
      value: newValue.value
    })
  }

  calculatePrice()
  resetForm()
}

/** Método que se ejecuta al momento de recibir una eliminación */
const deleteItem = (i: number) => {
  items.splice(i, 1)

  Swal.fire({
    text: 'Producto eliminado correctamente',
    icon: 'success',
    position: 'top',
    toast: true,
    timer: 4000,
    timerProgressBar: true,
    showConfirmButton: false,
    width: 520
  })

  calculatePrice()
  resetForm()
}

/** Calcula los precios del producto */
const calculatePrice = () => {
  // El método 'reduce' se encarga de reducir un array a un solo valor, acumulando los elementos a un solo valor
  // Funciona como un for en el sentido que itera todo el array y, en cada iteración, toma el valor de la propiedad 'quantity' y la suma al acumulador llamado 'acum'. El 0 es el valor inicial de 'acum'.
  let quantityTotal: number = items.reduce((acum, item) => acum + item.quantity, 0)
  totalBase.value = items.reduce((acum, item) => acum + item.value * item.quantity, 0)

  calculateDiscounts(quantityTotal)
  totalAbsolute.value = Math.floor(totalBase.value * (1 - discount.value / 100))
}

/** Calcula los descuentos con base a la lógica establecida
 * @param quantityTotal Toma la cantidad total de productos en la lista
 */
const calculateDiscounts = (quantityTotal: number) => {
  discount.value = 0

  if (totalBase.value >= 60000) {
    discount.value = 5
  }
  if (totalBase.value >= 120000) {
    discount.value = 10
  }
  if (totalBase.value >= 240000) {
    discount.value = 15
  }
  if (quantityTotal >= 12) {
    discount.value = 20
  } else if (quantityTotal >= 6) {
    discount.value = 10
  }
}

/** Reinicia los campos del formulario */
const resetForm = () => {
  newArticle.value = ''
  newQuantity.value = 1
  newValue.value = 1
}
</script>

<template>
  <!-- Formulario -->
  <form @submit.prevent="addItem" class="form">
    <label for="txtArticle" class="label">Artículo</label>
    <input id="txtArticle" type="text" v-model="newArticle" class="input" />
    <label for="txtQuantity" class="label">Cantidad</label>
    <input id="txtQuantity" type="number" v-model="newQuantity" class="input" />
    <label for="txtValue" class="label">Valor Unitario</label>
    <input id="txtValue" type="number" v-model="newValue" class="input" />
    <button type="submit" class="button">Agregar</button>
  </form>

  <!-- Contenedor tabla y factura -->
  <section class="dataContainer">
    <Table :items="items" @item-deleted="deleteItem" />

    <div class="showTotal">
      <label for="totalBase" class="totalLabel">Total compra:</label>
      <p id="totalBase" class="totalValue">${{ totalBase }}</p>
      <br />
      <label for="discount" class="totalLabel">Descuento:</label>
      <p id="discount" class="totalValue">{{ discount }}%</p>
      <br />
      <label for="total" class="totalLabel">Total a pagar:</label>
      <p id="total" class="totalValue">${{ totalAbsolute }}</p>
    </div>
  </section>
</template>

<style scoped>
.form {
  color: black;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px;
  background-color: #f0f0f0;
  border-radius: 8px;
  box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.2);
  width: calc(100% - 256px);
  margin-right: 32px;
  justify-self: end;
  align-self: center;
}

.label {
  font-size: 16px;
  margin-bottom: 4px;
}

.input {
  width: 100%;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 12px;
}

.button {
  width: 100%;
  padding: 12px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.3s ease;
}

.button:hover {
  background-color: #0056b3;
}

.totalLabel {
  font-size: 18px;
  margin-bottom: 4px;
}

.totalValue {
  font-size: 20px;
  font-weight: bold;
  color: #007bff;
}

.dataContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px;
  background-color: #f0f0f0;
  border-radius: 8px;
  box-shadow: 0px 0px 5px rgba(0, 0, 0, 0.2);
  color: black;
  width: calc(600px);
  margin-right: 32px;
  justify-self: start;
  overflow-y: auto;
  max-height: 80vh;
}

.showTotal {
  margin-top: 24px;
  align-self: flex-end;
}
</style>

