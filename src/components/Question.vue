<template>
  <div>
    <main v-cloak id="isStart">
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
    <Level :qIndex="qIndex" />
  </div>
</template>

<script>
import _ from "lodash";
import Level from "./Level";

export default {
  name: "Question",
  components: {
    Level
  },
  // props: [""],
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
        this.isStart = false;
      }
    }
  }
};
</script>

<style>
</style>