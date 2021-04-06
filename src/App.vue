<template>
  <div id="app">
    <Calendar/>
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'

export default {
  name: 'App',
  components: {
    Calendar
  }
}
</script>

<style lang="sass">
  body
    margin: 0

  #app
    height: 100vh
    background-color: #191919
</style>
