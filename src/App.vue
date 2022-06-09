<template>
   <main>
  <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount"/>
   <template v-if="this.question">
    <h1 v-html="this.question"></h1>
   <template v-for="(item, index) in this.answers" v-bind:key="index">
   <div class="area-input">
    <input type="radio" name="options" :value="item"  v-model="this.chosen_answer" :disabled="this.answer_submit"/>
    <label v-html="item"></label><br>
    </div>
   </template>
    <button class="send" @click="submit" v-if="!this.answer_submit"> Send</button>

    <section class="result" v-if="this.answer_submit">
        <h4 v-if="this.chosen_answer === this.correctAnswer" v-html="'&#9989; Congratulations, the answer ' + this.correctAnswer +  ' is correct.'"></h4>
        <h4 v-else v-html="'&#10060;  I´m sorry, you picked the wrong answer. The correct is ' + this.correctAnswer + '.' "></h4>
        <button class="send" type="button" @click="getNewQuestion()"> Next question</button>
    </section>
    </template>
    <template>

    </template>
  </main>
</template>
<script>
  import ScoreBoard from './components/ScoreBoard.vue'
export default {
    name: "App",
    data() {
        return {
            question: undefined,
            incorrectAnswers: undefined,
            correctAnswer: undefined,
            chosen_answer: undefined,
            answer_submit: false,
            winCount: 0,
            loseCount: 0
        };
    },
    methods: {
        submit() {
            if (!this.chosen_answer) {
                alert("Por favor, selecione uma opção.");
            }
            else {
                this.answer_submit = true;
                if (this.chosen_answer === this.correctAnswer) {
                   this.winCount++;
                }
                else {
                    this.loseCount++;
                }
            }
        },
        getNewQuestion() {
            this.answer_submit = false;
            this.chosen_answer = undefined;
            this.question = undefined;
            this.axios.get("https://opentdb.com/api.php?amount=1")
                .then((response) => {
                const questionResp = response.data.results[0].question;
                const wrongAnswers = response.data.results[0].incorrect_answers;
                const correctAnswerResp = response.data.results[0].correct_answer;
                this.question = questionResp;
                this.incorrectAnswers = wrongAnswers;
                this.correctAnswer = correctAnswerResp;
            });
        },
    },
    computed: {
        answers() {
            var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
            answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);
            return answers;
        },
    },
    created() {
        return this.getNewQuestion();
    },
    components: { ScoreBoard }
};


// https://opentdb.com/api.php?amount=1
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;


  main {
      display: flex;
      flex-direction: column;
  align-items: center;
  }

  input[type="radio"]{
    margin: 12px 4px;
  }

div.area-input{
  display: flex;
  align-items: center;
  justify-content: start;
}


  button.send {
    background-color: #2c3e50;
    border: 1px solid #2c3e50;
    min-width: 120px;
    height: 40px;
    margin-top: 12px;
    cursor: pointer;
    color: white; 
    border-radius: 10px;
  }
}

</style>
