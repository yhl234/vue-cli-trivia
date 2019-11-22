<template>
	<div
		id="app"
		@keyup.enter="start()"
		@keyup.65="isAnswer(0)"
		@keyup.66="isAnswer(1)"
		@keyup.67="isAnswer(2)"
		@keyup.68="isAnswer(3)"
		tabindex="0"
	>
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
			<button @click="start()">
				Press Enter to Start
			</button>
		</main>
		<Level />
		<aside v-cloak v-if="isStart">
			<section>
				<button id="fifty_fifty">
					<i class="ms-Icon ms-Icon--Contrast" aria-hidden="true"></i>
				</button>
				<button id="ask_audience">
					<i class="ms-Icon ms-Icon--Group" aria-hidden="true"></i>
				</button>
				<button id="phone_friend">
					<i class="ms-Icon ms-Icon--Phone" aria-hidden="true"></i>
				</button>
			</section>
		</aside>
	</div>
</template>

<script>
import _ from "lodash";
import Level from "./Level";

export default {
	name: "Triva",
	components: { Level },
	data: function() {
		return {
			isStart: false,
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
		});
	},
	watch: {
		qIndex() {
			this.displayQuestion();
		}
	},
	methods: {
		playSound(sound) {
			const path = `@/assets/sounds/${sound}.ogg`;
			const audio = new Audio(path);
			audio.play();
		},
		read() {
			const speech = new SpeechSynthesisUtterance();
			speechSynthesis.cancel();
			speech.lang = "en-US";
			speech.text = this.currentQuestion.question;
			speechSynthesis.speak(speech);
		},
		start() {
			this.isStart = true;
			const sound = `Round1`;
			this.playSound(sound);
			this.displayQuestion();
		},
		active(level) {
			if (level === this.qIndex + 1) {
				return "active";
			}
		},
		displayQuestion() {
			this.currentQuestion = this.questions[this.qIndex];
			this.read();
		},
		isAnswer(index) {
			this.currentAnswer = this.currentQuestion.correct_answer;
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
#app ul {
	list-style-type: none;
	color: #cad622;
	font-size: 20px;
	width: 100%;
	padding: 0;
	margin: 0;
}

#app li {
	margin-top: 10px;
	cursor: pointer;
}
#app li:nth-child(1),
li:nth-child(6),
li:nth-child(11) {
	color: #fff;
}
#app li span {
	margin-left: 30px;
}
#isStart {
	flex-grow: 5;
}
#isStart {
	display: grid;
	grid-template-columns: 50%, 50%;
	grid-template-rows: 60%, 20%, 10%, 10%;
	grid-template-areas: "header header" "question question" "a b" "c d";
	grid-column-gap: 10px;
	grid-row-gap: 15px;
}
#start {
	display: grid;
	height: 100vh;
	width: 100vw;
	align-items: center;
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

.active {
	border-top: 1px solid skyblue;
	border-bottom: 1px solid skyblue;
}

main button {
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
main button div {
	display: block;
}
main button span:first-child {
	color: #5b6102;
}

main button #btnAnswer0 {
	grid-area: a;
}
main button #btnAnswer1 {
	grid-area: b;
}
main button #btnAnswer2 {
	grid-area: c;
}
main button #btnAnswer3 {
	grid-area: d;
}
.final_answer {
	background: #709634 !important;
}
.invisible {
	visibility: hidden !important;
}
.ghost {
	display: none;
}

aside {
	/* flex-grow: 1; */
	display: flex;
	flex-direction: column;
	background-color: rgba(0, 0, 0, 0.5);
	height: 100%;
}

aside i {
	font-size: 80px;
}
aside section {
	display: flex;
	justify-items: center;
	text-align: center;
}

aside button {
	cursor: pointer;
	border-radius: 10px 20px;
	margin: 10px;
	color: #fff;

	/* taken from https://uigradients.com/#Lawrencium */
	background: #000428; /* fallback for old browsers */
	background: -webkit-linear-gradient(
		to bottom,
		#004e92,
		#000428
	); /* Chrome 10-25, Safari 5.1-6 */
	background: linear-gradient(to bottom, #004e92, #000428);
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
