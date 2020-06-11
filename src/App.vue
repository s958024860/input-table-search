<template>
  <div id="app">
    <FormTest />
<!--    <Test />-->
  </div>
</template>

<script>
import FormTest from './components/FormTest.vue'
// import Test from './components/Test.vue'

export default {
  name: 'App',
  components: {
    FormTest,
    // Test,
  }
}
</script>

<style>
  html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  #app {
    height: 100%;
    display: flex;
  }
</style>
