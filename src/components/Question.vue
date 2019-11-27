<template>
  <main v-cloak>
    <div id="game" :key="componentKey">
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
    <Level :qIndex="qIndex" @emitFifty="fifty()" />
  </main>
</template>

<script>
import _ from "lodash";
import Level from "./Level";
const round1 = new Audio(require(`@/assets/sounds/Round1.ogg`));
const round2 = new Audio(require(`@/assets/sounds/Round2.ogg`));
const round3 = new Audio(require(`@/assets/sounds/Round3.ogg`));

export default {
  name: "Question",
  components: {
    Level
  },
  props: ["isStart", "keyUp"],
  data: function() {
    return {
      // An array of question object
      questions: [],
      // Watch()
      qIndex: 0,
      // Object from questions[qIndex]
      currentQuestion: "",
      // The answer of current question
      currentAnswer: "",
      // whichRound()
      round: "round1",
      // vote()
      isVoting: false,
      // for fifty to rerender the button
      componentKey: 0,
      test: {}
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
  mounted() {
    this.whichRound();
    this.bgm();
  },
  watch: {
    qIndex() {
      this.displayQuestion();
      this.whichRound();
      this.read();
    },
    round() {
      this.bgm();
    },
    keyUp() {
      this.isAnswer(this.keyUp);
    }
  },
  methods: {
    // ---------------------------------BONUS 2
    //
    //
    // determine which bgm should be played
    bgm() {
      if (this.round === "round3") {
        round3.play();
      } else if (this.round === "round2") {
        round2.play();
      } else if (this.round === "round1") {
        round1.play();
      }
    },
    whichRound() {
      // once qIndex reach certian point, gp tp next round and pause previous bgm
      if (this.qIndex === 10) {
        this.round = "round3";
        round2.pause();
      } else if (this.qIndex === 5) {
        this.round = "round2";
        round1.pause();
      } else if (this.qIndex === 0) {
        this.round = "round1";
      }
    },
    playSound(sound) {
      // file extension should include
      const path = require(`@/assets/sounds/${sound}`);
      const audio = new Audio(path);
      audio.play();
    },
    read() {
      speechSynthesis.cancel();
      const speech = new SpeechSynthesisUtterance();
      const voices = window.speechSynthesis.getVoices();
      speech.voice = voices[0];
      speech.lang = "en-US";
      speech.text = this.currentQuestion.question;
      window.speechSynthesis.speak(speech);
      this.test = speech;
    },
    displayQuestion() {
      this.currentQuestion = this.questions[this.qIndex];
      this.currentAnswer = this.currentQuestion.correct_answer;
      this.read();
    },
    isAnswer(index) {
      const selected = this.currentQuestion.answers.indexOf(this.currentAnswer);
      if (index === selected) {
        this.playSound("RightAnswerShort.ogg");
        this.qIndex++;
      } else {
        this.playSound("WrongAnswer.ogg");
        this.emitStart();
      }
    },
    recognition() {
      window.SpeechRecognition =
        window.SpeechRecognition || window.webkitSpeechRecognition;
      const recognition = new window.SpeechRecognition();
      recognition.interimResults = false;
      recognition.lang = "en-US";
      recognition.addEventListener("result", e => {
        let transcript = e.results[0][0].transcript;
        switch (transcript) {
          case "a final answer":
            this.isAnswer("a");
            break;
          case "b final answer":
            this.isAnswer("b");

            break;
          case "c final answer":
            this.isAnswer("c");

            break;
          case "d final answer":
            this.isAnswer("d");
            break;
        }
        recognition.addEventListener("end", recognition.start);
        recognition.start();
      });
    },
    emitStart() {
      this.$emit("update:isStart", false);
    },
    vote() {
      this.isVoting = !this.isVoting;
    },
    fifty() {
      // shuffle and pop to remove incorrent answers randomly
      let inAns = this.currentQuestion.incorrect_answers;
      _.shuffle(inAns);
      inAns.pop();
      inAns.pop();
      // rebuild current answers
      this.currentQuestion.incorrect_answers = inAns;
      this.currentQuestion.answers = [];
      this.currentQuestion.answers.push(...inAns);
      this.currentQuestion.answers.push(this.currentAnswer);
      // rerender the button
      this.componentKey += 1;
    }
  }
};
</script>

<style>
main {
  display: flex;
  height: 100vh;
}
#game {
  display: grid;
  grid-template-columns: 50%, 50%;
  grid-template-rows: 60%, 20%, 10%, 10%;
  grid-template-areas: "question question" "a b" "c d";
  grid-column-gap: 10px;
  grid-row-gap: 15px;
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
