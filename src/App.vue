<template>
  <div id="app">
    <QuestionView></QuestionView>
  </div>
</template>

<script>
import QuestionView from "./components/QuestionView.vue";
// import MiniAppPostOverlay  from "./components/MiniAppPostOverlay"
export default {
  name: 'App',
  components: {
    QuestionView
    // MiniAppPostOverlay
  }
  // ,
  // data: () => {
  //   return {
  //
  //   }
  // }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
