<script lang='ts' setup>
import ListItem from './ListItem.vue'
import { ref, onMounted, computed } from 'vue'
import type { Ref } from 'vue'

// Definimos el tipo para un elemento de la lista
type Item = {
  title: string,
  checked?: boolean
}

// Función para escribir en localStorage
const setToStorage = (items: Item[]): void => {
  localStorage.setItem('list-items', JSON.stringify(items))
}

// Función para leer desde localStorage
const getFromStorage = (): Item[] | [] => {
  const stored = localStorage.getItem('list-items')
  if (stored) {
    return JSON.parse(stored)
  }
  return []
}

// Inicializamos los elementos de la lista desde localStorage
const initListItems = (): void => {
  if (storageItems.value?.length === 0) {
    const listItems = [
      { title: 'Make a todo list app', checked: true },
      { title: 'Predict the weather', checked: false },
      { title: 'Read some comics', checked: false },
      { title: 'Let\'s get cooking', checked: false },
      { title: 'Pump some iron', checked: false },
      { title: 'Track my expenses', checked: false },
      { title: 'Organise a game night', checked: false },
      { title: 'Learn a new language', checked: false },
      { title: 'Publish my work' }
    ]
    setToStorage(listItems)
    storageItems.value = listItems
  }
}

// Creamos una referencia reactiva para los elementos de la lista
const storageItems: Ref<Item[]> = ref([])

// Ejecutamos la función de inicialización al montar el componente
onMounted(() => {
  initListItems()
  storageItems.value = getFromStorage()
})

// Función para actualizar el estado de una tarea
const updateItem = (item: Item): void => {
  const updatedItem = findItemInList(item)
  if (updatedItem) {
    toggleItemChecked(updatedItem)
    setToStorage(storageItems.value)
  }
}

// Función para buscar un elemento en la lista
const findItemInList = (item: Item): Item | undefined => {
  return storageItems.value.find((itemInList: Item) => itemInList.title === item.title)
}

// Función para cambiar el estado de una tarea
const toggleItemChecked = (item: Item): void => {
  item.checked = !item.checked
}

// Función para ordenar la lista con los elementos completados al final
const sortedList = computed(() =>
  [...storageItems.value].sort((a, b) => (a.checked ? 1 : 0) - (b.checked ? 1 : 0))
)
</script>

<template>
  <ul>
    <li v-for='(item, key) in sortedList' :key='key'>
      <ListItem :is-checked='item.checked' @click.prevent="updateItem(item)">
        {{ item.title }}
      </ListItem>
    </li>
  </ul>
</template>

<style scoped>
ul {
  list-style: none;
}
li {
  margin: 0.4rem 0;
}
</style>
