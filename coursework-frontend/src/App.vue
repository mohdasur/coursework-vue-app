<script setup>
import LessonsPage from './components/LessonsPage.vue'
import lessons from './assets/lessons.json'
import {ref} from 'vue'
const lessonsList = ref(lessons)
</script>

<template>
  <header>
    
  </header>

  <main>
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
