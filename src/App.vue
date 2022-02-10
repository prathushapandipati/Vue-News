<template>
  <div id="app">
    <News :apiKey="apiKey" />
  </div>
</template>

<script>
import News from './components/News'

export default {
  name: 'App',
  components: {
    News,
  },
  data() {
    return {
      apiKey: '66d18699cab244c9a7b673f819863874',
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
}
</style>
