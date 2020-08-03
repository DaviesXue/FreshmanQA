<template>
    <div id="app">
        <img src="header.png" class="header">
        <QuestionView></QuestionView>
    </div>
</template>

<script>
  import QuestionView from "./components/QuestionView.vue";

  export default {
    name: 'App',
    components: {
        QuestionView
    }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    max-width: 600px;
    margin: 0 auto;
  }
</style>
