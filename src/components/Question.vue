<template>
  <main v-cloak id="isStart" v-if="isStart">
    <div v-cloak class="header">
      <!-- Place to place messages or voting results etc -->
    </div>
    <div v-cloak class="question">
      <h1 v-html="currentQuestion.question"></h1>
    </div>

    <button
      v-cloak
      v-for="(a, index) in currentQuestion.answers"
      :key="index"
      :id="'btnAnswer' + index"
      @click="isAnswer(index)"
    >
      <div v-if="index === 0">
        <span>A:</span>
        <span v-html="a"></span>
      </div>
      <div v-else-if="index === 1">
        <span>B:</span>
        <span v-html="a"></span>
      </div>
      <div v-else-if="index === 2">
        <span>C:</span>
        <span v-html="a"></span>
      </div>
      <div v-else-if="index === 3">
        <span>D:</span>
        <span v-html="a"></span>
      </div>
    </button>
  </main>
  <main v-else id="start">
    <button @click="start()">Press Enter to Start</button>
  </main>
</template>

<script>
export default {
  name: "Question",
  props: ["isStart", "currentQuestion"],
  // data: function() {
  //   return {
  //     isStart: this.isStart
  //   };
  // },
  methods: {}
};
</script>

<style>
</style>