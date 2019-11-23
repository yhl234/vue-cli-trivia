<template>
  <aside v-cloak>
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
    <section>
      <ul>
        <li
          v-for="(m, index) in money"
          :key="index"
          :class="{ active: m.level == qIndex + 1 }"
        >{{ m.level }} - {{ m.amount }}</li>
      </ul>
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
export default {
  name: "Level",
  props: ["qIndex"],
  data: function() {
    return {
      money
    };
  },
  methods: {
    // active(level) {
    //   if (level === this.qIndex + 1) {
    //     return "active";
    //   }
    // }
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
  cursor: pointer;
}
li:nth-child(1),
li:nth-child(6),
li:nth-child(11) {
  color: #fff;
}
li span {
  margin-left: 30px;
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
</style>
