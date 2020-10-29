<template>
    <div class="question-box-container">
        <b-jumbotron>
         
          <template #lead>
            {{ currentQuestion.question }}
          </template>

          <hr class="my-4">

          <b-list-group class="list-group">
            <b-list-group-item
              :class="answerClass(index)"
              @click="selectAnswer(index)"
              v-for="(answer,index) in answers"
              :key="index"
            >
              {{answer}}
            </b-list-group-item>
          </b-list-group>

          <b-button variant="primary" @click="submitAnswer" :disabled="selectedIndex === null || answered">
            Submit
          </b-button>
          <b-button @click="next" variant="success" href="#">Next</b-button>
        </b-jumbotron>
    </div>

</template>

<script>
import _ from 'lodash';
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return { 
      selectedIndex: null,
      shuffledAnswers: [],
      answered: false,
      correctIndex: null
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers()
      }
    }
  },
  methods: {
    submitAnswer() {
      let isCorrect = false;
     
      
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }

      this.answered = true;
      this.increment(isCorrect);
    },
    selectAnswer(index) {
      this.selectedIndex = index;
      console.log(index);
    },

    answerClass(index){
      let answerClass = '';

      if( !this.answered && this.selectedIndex === index) {
        answerClass = 'selected';
      }
      else if ( this.answered && this.correctIndex === index ) {
         answerClass = 'correct';
      }
      else if ( this.answered && this.selectedIndex === index && this.correctIndex !== index ) {
        answerClass = 'incorrect';
      }
      
      return answerClass
    },

    shuffleAnswers() {
      let answerArray = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer];
      this.shuffledAnswers = _.shuffle(answerArray);
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
    }
  },
  computed: {
    answers() {
      let answerArray = [...this.currentQuestion.incorrect_answers];
      answerArray.push(this.currentQuestion.correct_answer);
      return answerArray;
    }
  }
}
</script>


<style scoped>
  .list-group {
    margin-bottom: 15px;
  }

  .list-group-item:hover{
    background: #eee;
    cursor: pointer;
  }

  .selected{ 
    background-color: lightskyblue;
  }

  .correct{ 
    background-color: lightgreen;
  }

  .incorrect{ 
    background-color: lightsalmon;
  }
  .btn {
    margin: 0 5px;
  }
</style>