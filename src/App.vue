<template>

<div>

    <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount"/>

    <template v-if="this.question">
      <h1 v-html="this.question" />

        <template v-for="(answer, index) in this.answers" v-bind:key="index">
          <input 
            :disabled="this.answerSubmitted" 
            type="radio" name="options" 
            :value="answer" 
            v-model="this.chosen_answer">
            
          <label v-html="answer"></label><br>
        </template>

      <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" class="send" type="button"> Confirmar </button>
    </template>

    <template v-if="!this.question">
      <div class="loader"></div>
    </template>

    <section class="result" v-if="this.answerSubmitted">
      <template v-if="this.chosen_answer == this.correctAnswer">
        <h4
          v-html=" '&#9989; Parabéns, a resposta ' +  this.correctAnswer + ' está correta.' ">
        
        </h4>
      </template>
      <template v-else>
        <h4
          v-html=" '&#10060;  Que pena, a resposta está errada. A resposta correta é ' + this.correctAnswer + '.' ">
        
        </h4>
      </template>
      <button @click="this.getNewQuestion()" class="send" type="button">Próxima pergunta</button>
    </section>

</div>

</template>

<script>

import ScoreBoard from '@/components/ScoreBoard.vue';

export default {
  name: 'App',
  components: {
    ScoreBoard
  },
  data() {
    return {
      chosen_answer: undefined,
      question: undefined,
      incorrectAnswers: [],
      correctAnswer: '',
      winCount: 0,
      loseCount: 0,
      answerSubmitted: false
    }
  },
  computed: {
      answers() {
        var answers = [...this.incorrectAnswers];
        answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
        return answers;
      }
  },
  methods: {
      submitAnswer() {
      if (!this.chosen_answer) {
        alert('Pick one of the options');
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++
        }
      }
    },
    getNewQuestion() {
      this.chosen_answer = undefined;
      this.answerSubmitted = false;
      this. question = undefined;

      this.axios
      .get('https://opentdb.com/api.php?amount=1&category=18')
      .then(response => (
        this.question = response.data.results[0].question,
        this.incorrectAnswers = response.data.results[0].incorrect_answers,
        this.correctAnswer = response.data.results[0].correct_answer
      )).catch(function (error) {
        console.log(error);
      });
    }
  },
  created() {
    this.getNewQuestion();
  },
}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

}

h1 {
  margin-top: 40px;
}

input[type='radio']{
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
}

.loader {
  border: 16px solid #f3f3f3;
  border-radius: 50%;
  border-top: 16px solid blue;
  border-bottom: 16px solid blue;
  width: 120px;
  height: 120px;
  -webkit-animation: spin 2s linear infinite;
  animation: spin 2s linear infinite;
  margin: 60px auto;
}

@-webkit-keyframes spin {
  0% { -webkit-transform: rotate(0deg); }
  100% { -webkit-transform: rotate(360deg); }
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
