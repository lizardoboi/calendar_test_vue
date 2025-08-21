<template>
  <div id="app">
    <h2>Мой календарь</h2>
    <Calendar v-model="selected" locale="ru" />
    <p>Выбрано: {{ selected }}</p>
  </div>
</template>

<script>
import Calendar from './components/calendar.vue'

export default {
  components: { Calendar },
  data: () => ({
    selected: null
  })
}
</script>