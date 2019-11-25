<template>
  <div
    @keyup.enter="start()"
    @keyup.65="passKeyUp(0)"
    @keyup.66="passKeyUp(1)"
    @keyup.67="passKeyUp(2)"
    @keyup.68="passKeyUp(3)"
    tabindex="0"
  >
    <Question v-if="isStart" :isStart.sync="isStart" :keyUp="keyUp" />
    <StartButton v-else :isStart.sync="isStart" />
  </div>
</template>

<script>
import Question from "./Question";
import StartButton from "./StartButtton";

export default {
  name: "Triva",
  components: {
    Question,
    StartButton
  },
  data: function() {
    return {
      isStart: false,
      keyUp: ""
    };
  },

  methods: {
    passKeyUp(index) {
      this.keyUp = index;
    }
  }
};
</script>

<style>
#game {
  flex-grow: 5;
}
aside {
  flex-grow: 1 0 0;
  display: flex;
  flex-direction: column;
  background-color: rgba(0, 0, 0, 0.5);
  height: 100%;
}

.final_answer {
  background: #709634 !important;
}
.invisible {
  visibility: hidden !important;
}

.settings {
  margin-top: 100px;
}
.settings button {
  background: grey;
  margin: 3px;
  border-radius: 0 0 0 0;
}
.settings button i {
  font-size: 24px;
}

.header h1 {
  text-align: center;
  font-size: 64px;
}
</style>
