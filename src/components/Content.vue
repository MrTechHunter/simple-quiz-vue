<template>
  <div>
    <b-jumbotron>
      <template>{{ currentQuestion.question }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, i) in answers"
          :key="i"
          @click="changeIndex(i)"
          :class="correctFunc(i)"
        >
          {{ answer }}
        </b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        @click="submitAnswer"
        :disabled="currentIndex == null || answered"
        href="#"
        >Submit</b-button
      >
      <b-button variant="success" href="#" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.currentIndex = null;
        this.answered = null;
        this.correctAnswer = null;
        this.shuffleAnswers();
      },
    },
  },
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      currentIndex: null,
      shuffledAnswers: [],
      correctAnswer: null,
      answered: false,
    };
  },
  methods: {
    changeIndex(i) {
      this.currentIndex = i;
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctAnswer = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    submitAnswer() {
      let is_correct = false;
      if (this.currentIndex === this.correctAnswer) {
        is_correct = true;
      }
      this.answered = true;
      this.increment(is_correct);
    },
    correctFunc(index) {
      let answerClass = "";
      if (!this.answered && this.currentIndex === index) {
        answerClass = "selected";
      } else if (this.answered && index === this.correctAnswer) {
        answerClass = "correct";
      } else if (
        this.answered &&
        index === this.currentIndex &&
        index !== this.correctAnswer
      ) {
        answerClass = "incorrect";
      }

      return answerClass;
    },
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
};
</script>

<style scoped>
.list-group-item:hover {
  background-color: #eee;
  cursor: pointer;
}
.btn {
  margin: 10px 2px;
}
.selected {
  background-color: lightblue;
}
.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: red;
}
</style>