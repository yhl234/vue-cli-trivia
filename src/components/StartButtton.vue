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
    }
  }
};
</script>

<style>
</style>