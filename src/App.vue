<template>
  <div id="app">

    <Screencapturer />
    <Gallery />

  </div>
</template>

<script>
import Screencapturer from './components/Screencapturer.vue'
import Gallery from './components/Gallery.vue'

export default {
  name: 'App',
  components: {
    Screencapturer, 
    Gallery
  }, 
  methods: {

  }
}

</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100;400;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  transition: all 0.3s ease-in-out;
}

#app {
  font-family: Roboto, Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: 0px;
  color: #444;
}
</style>
