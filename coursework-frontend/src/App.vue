<script setup>
import SortBar from './components/SortBar.vue'
import LessonsPage from './components/LessonsPage.vue'
import lessons from './assets/lessons.json'
import {ref, watch} from 'vue'
const lessonsList = ref(lessons)
const sortOrder = ref('asc')
const sortField = ref('none')
const updateSort = ({field, order}) => {
  sortField.value = field
  sortOrder.value = order
}
const addToCart = (lessonId) => {
  const index = lessonsList.value.findIndex(lesson => lesson.id === lessonId)
  if(index === -1)
    return

  const updatedLessons = [...lessonsList.value]
  if (updatedLessons[index].Spaces > 0) {
    updatedLessons[index].Spaces -= 1

    // // Check if the lesson is already in the cart
    // const cartIndex = cart.value.findIndex(item => item.Sport === updatedLessons[index].Sport)

    // if (cartIndex !== -1) {
    //   // Increase quantity if it's already in the cart
    //   cart.value[cartIndex].quantity += 1
    // } else {
    //   // Add new item to cart with quantity 1
    //   cart.value.push({ ...updatedLessons[index], quantity: 1 })
    // }
  }
  lessonsList.value = updatedLessons
}

watch([sortOrder, sortField, lessonsList], () => {
  
  
  if(sortField.value == 'none')
    return

  lessonsList.value.sort((a, b) => {
    let compareA = a[sortField.value]
    let compareB = b[sortField.value]

    // Handle numeric sorting for fields like Price and Spaces
    if (sortField.value === 'Price') {
      compareA = parseInt(compareA.replace('£', '')) || compareA
      compareB = parseInt(compareB.replace('£', '')) || compareB
    }
    else if(sortField.value === 'Spaces'){
      compareA = parseInt(compareA) || compareA
      compareB = parseInt(compareB) || compareB
    }

    if (sortOrder.value === 'asc') {
      return compareA > compareB ? 1 : -1
    } else {
      return compareA < compareB ? 1 : -1
    }
  })})

</script>

<template>

  <main>
    <SortBar @updateSort="updateSort"/>
    <LessonsPage :lessons="lessonsList" @addToCart="addToCart" />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
