<template>
  <div>
    <div class="question" v-for="(item, i) of history" :key="item.qid">
      {{ i + 1 }}. {{ form[item.qid].text }}
      <div class="answers">
        <span
          v-for="answer in form[item.qid].answers"
          :key="answer.text"
          @click="selectAnswer(i, answer)"
          :class="[answer.text == item.answer ? 'selectedAnswer' : '', 'answer']"
        >{{ answer.text }}</span>
      </div>
    </div>
    <div class="result">{{ result }}</div>
  </div>
</template>

<script>
const yaml = require("js-yaml");

export default {
  name: "Home",
  components: {},
  data() {
    return {
      form: {},
      history: [],
      result: ""
    };
  },
  methods: {
    selectAnswer(q, answer) {
      if (q + 1 !== this.history.length) {
        this.history = this.history.slice(0, q + 1);
        this.result = "";
      }
      this.$set(this.history[q], "answer", answer.text);
      if (Number.isInteger(answer.goto)) {
        this.history.push({ qid: answer.goto });
      } else {
        this.result = answer.goto;
      }
    }
  },
  mounted() {
    fetch("/form.yml")
      .then(response => response.text())
      .then(data => {
        this.form = yaml.safeLoad(data);
        this.history = [{ qid: 1 }];
      });
  }
};
</script>

<style lang="scss" scoped>
.answer {
  background-color: lightblue;
  padding: 12px;
  margin: 12px;
  cursor: pointer;
}

.selectedAnswer {
  background-color: pink;
}

.answers {
  padding-top: 24px;
}

.question {
  padding-top: 48px;
}

.result {
  padding-top: 48px;
  font-weight: bold;
}
</style>
