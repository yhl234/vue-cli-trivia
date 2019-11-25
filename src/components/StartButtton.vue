<template>
  <main id="start">
    <button @click="emitStart()">Press Enter to Start</button>
  </main>
</template>

<script>
export default {
  name: "StartButton",
  props: ["isStart"],
  methods: {
    emitStart() {
      this.$emit("update:isStart", !this.isStart);
      this.displayQuestion();
    }
  }
};
</script>

<style>
#start {
  display: grid;
  height: 100vh;
  width: 100vw;
  align-items: center;
}
</style>