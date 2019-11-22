<template>
	<div id="app">
		<Trivia />
		<MyChart pollresults="[2,5,6,0]" />
	</div>
</template>

<script>
import MyChart from "./components/MyChart.vue";
import Trivia from "./components/Trivia";

export default {
	name: "app",
	components: {
		MyChart,
		Trivia
	}
};
</script>

<style>
** {
	box-sizing: border-box;
}
[v-cloak] {
	display: none;
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
