<template>
  <aside>
    <section>
      <button id="fifty_fifty" @click="emitFifty" :class="{ghost:fifty}" :disabled="isFifty">
        <i class="ms-Icon ms-Icon--Contrast" aria-hidden="true"></i>
      </button>
      <button id="ask_audience" @click="vote">
        <i class="ms-Icon ms-Icon--Group" aria-hidden="true"></i>
      </button>
      <button id="phone_friend">
        <i class="ms-Icon ms-Icon--Phone" aria-hidden="true"></i>
      </button>
    </section>
    <section>
      <ul v-if="!isVoting">
        <li
          v-for="(m, index) in money"
          :key="index"
          :class="{ active: m.level == qIndex + 1 }"
        >{{ m.level }} - {{ m.amount }}</li>
      </ul>
      <MyChart v-else />
    </section>
  </aside>
</template>

<script>
const money = [
  { level: "15", amount: "1,000,000" },
  { level: "14", amount: "500,000" },
  { level: "13", amount: "250,000" },
  { level: "12", amount: "100,000" },
  { level: "11", amount: "50,000" },
  { level: "10", amount: "25,000" },
  { level: "9", amount: "16,000" },
  { level: "8", amount: "8,000" },
  { level: "7", amount: "4,000" },
  { level: "6", amount: "2,000" },
  { level: "5", amount: "1,000" },
  { level: "4", amount: "500" },
  { level: "3", amount: "300" },
  { level: "2", amount: "200" },
  { level: "1", amount: "100" }
];
import MyChart from "./MyChart.vue";
export default {
  name: "Level",
  components: {
    MyChart
  },
  props: ["qIndex"],
  data: function() {
    return {
      money,
      isVoting: false,
      fifty: false
    };
  },
  computed: {
    isFifty() {
      return this.fifty;
    }
  },
  methods: {
    vote() {
      this.isVoting = !this.isVoting;
    },
    emitFifty() {
      this.$emit("emitFifty");
      this.fifty = true;
    }
  }
};
</script>

<style>
.active {
  border-top: 1px solid skyblue;
  border-bottom: 1px solid skyblue;
}
ul {
  list-style-type: none;
  color: #cad622;
  font-size: 20px;
  width: 100%;
  padding: 0;
  margin: 0;
}

li {
  margin-top: 10px;
}
li:nth-child(1),
li:nth-child(6),
li:nth-child(11) {
  color: #fff;
}
li span {
  margin-left: 30px;
}
aside {
  height: 100vh;
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
.ghost {
  background: #808080;
  cursor: not-allowed;
}
</style>
