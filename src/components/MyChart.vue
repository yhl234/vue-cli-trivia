<template>
	<canvas id="myChart" width="200" height="100"></canvas>
</template>

<script>
import Chart from "chart.js";
export default {
	name: "MyChart",
	mounted() {
		const ctx = document.getElementById("myChart").getContext("2d");

		this.myChart = new Chart(ctx, {
			type: "bar",
			data: {
				labels: ["A", "B", "C", "D"],
				datasets: [
					{
						label: "votes",
						data: this.pollresults,
						backgroundColor: [
							"rgba(255, 99, 132, 0.2)",
							"rgba(54, 162, 235, 0.2)",
							"rgba(255, 206, 86, 0.2)",
							"rgba(75, 192, 192, 0.2)"
						],
						borderColor: [
							"rgba(255, 99, 132, 1)",
							"rgba(54, 162, 235, 1)",
							"rgba(255, 206, 86, 1)",
							"rgba(75, 192, 192, 1)"
						],
						borderWidth: 1
					}
				]
			},
			options: {
				scales: {
					yAxes: [
						{
							ticks: {
								beginAtZero: true
							}
						}
					]
				}
			}
		});
	},
	props: ["pollresults"],
	data: function() {
		return {
			myChart: null
		};
	},
	watch: {
		pollresults() {
			this.myChart.data.datasets[0].data = this.pollresults;
			this.myChart.update();
		}
	}
};
</script>

<style></style>
