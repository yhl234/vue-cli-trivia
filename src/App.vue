<template>
  <div id="app">
    <Trivia />
  </div>
</template>

<script>
import Trivia from "./components/Trivia";

export default {
  name: "app",
  components: {
    Trivia
  }
};
</script>

<style>
** {
  box-sizing: border-box;
}
[v-cloak] {
  display: none !important;
}

body {
  background-image: url("./assets/img/millionaire01.jpg");
  background-size: cover;
  background-color: #000;
  font-family: Arial, sans-serif;
  color: #fff;
  margin: 0;
}
</style>
