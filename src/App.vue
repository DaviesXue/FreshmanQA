<template>
    <div id="app">
        <img src="header.png" class="header">
        <QuestionView></QuestionView>
        <MiniAppPostOverlay></MiniAppPostOverlay>
    </div>
</template>

<script>
  import QuestionView from "./components/QuestionView.vue";
  import MiniAppPostOverlay from "./components/MiniAppPostOverlay";

  export default {
    name: 'App',
    components: {
        QuestionView,
        MiniAppPostOverlay
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
