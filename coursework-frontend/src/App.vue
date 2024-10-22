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

watch([sortOrder, sortField], () => {
  
  console.log(sortField.value)
  console.log(sortOrder.value)
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
    <LessonsPage :lessons="lessonsList" />
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
