<template >
  <main v-cloak>
    <div id="game">
      <div class="header">
        <!-- Place to place messages or voting results etc -->
      </div>
      <div v-cloak class="question">
        <h1 v-html="currentQuestion.question"></h1>
      </div>

      <button
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
    </div>
    <Level :qIndex="qIndex" />
  </main>
</template>

<script>
import _ from "lodash";
import Level from "./Level";

export default {
  name: "Question",
  components: {
    Level
  },
  props: ["isStart"],
  data: function() {
    return {
      // An array of question object
      questions: [],
      // Watch()
      qIndex: 0,
      // Object from questions[qIndex]
      currentQuestion: "",
      // The answer of current question
      currentAnswer: ""
    };
  },
  async created() {
    const res = await fetch(
      "https://opentdb.com/api.php?amount=15&type=multiple"
    );
    const json = await res.json();
    this.questions = json.results;
    // Combine answers to an array
    this.questions.forEach(question => {
      const incorrectAnswers = question.incorrect_answers;
      const correctAnswer = question.correct_answer;
      // Copy from it instead of reference it
      const answers = [...incorrectAnswers];
      answers.push(correctAnswer);
      question.answers = _.shuffle(answers);
      this.displayQuestion();
    });
  },
  beforeMount() {},

  watch: {
    qIndex() {
      this.displayQuestion();
    }
  },
  methods: {
    read() {
      const speech = new SpeechSynthesisUtterance();
      speechSynthesis.cancel();
      speech.lang = "en-US";
      speech.text = this.currentQuestion.question;
      speechSynthesis.speak(speech);
    },
    displayQuestion() {
      this.currentQuestion = this.questions[this.qIndex];
      this.currentAnswer = this.currentQuestion.correct_answer;
      this.read();
    },
    isAnswer(index) {
      const selected = this.currentQuestion.answers.indexOf(this.currentAnswer);
      if (index === selected) {
        this.qIndex++;
      } else {
        this.emitStart();
      }
    },
    emitStart() {
      this.$emit("update:isStart", false);
    }
  }
};
</script>

<style >
main {
  display: flex;
}
#game {
  display: grid;
  grid-template-columns: 50%, 50%;
  grid-template-rows: 60%, 20%, 10%, 10%;
  grid-template-areas: "header header" "question question" "a b" "c d";
  grid-column-gap: 10px;
  grid-row-gap: 15px;
}
.header {
  grid-area: header;
}
.question {
  grid-area: question;
  /* justify-self: center; */
  border: 1px solid white;
  padding: 50px 10%;
  /* taken from https://uigradients.com/#Lawrencium */
  background: #0f0c29;
  /* fallback for old browsers */
  background: -webkit-linear-gradient(to right, #24243e, #302b63, #0f0c29);
  /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to right, #24243e, #302b63, #0f0c29);
}

#game button,
#start button {
  cursor: pointer;
  display: block;
  justify-self: center;
  font-size: 24px;
  width: 95%;
  padding: 40px 0;
  color: #fff;
  /* taken from https://uigradients.com/#Lawrencium */
  background: #0f0c29;
  /* fallback for old browsers */
  background: -webkit-linear-gradient(to bottom, #24243e, #302b63, #0f0c29);
  /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to bottom, #24243e, #302b63, #0f0c29);
}
#game button div {
  display: block;
}
#game button span:first-child {
  color: #5b6102;
}

#btnAnswer0 {
  grid-area: a;
}
#btnAnswer1 {
  grid-area: b;
}
#btnAnswer2 {
  grid-area: c;
}
#btnAnswer3 {
  grid-area: d;
}
</style>