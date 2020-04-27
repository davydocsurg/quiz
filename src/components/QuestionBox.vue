<template>
<div>
  <b-jumbotron>

    <template v-slot:lead>
        {{currentQuestion.question}}
    </template>

    <hr class="my-4">
        <b-list-group>
            <b>
         <b-list-group-item v-for="(answer, index) in answers"
            @click="selectAnswer(index)" :key="index"
            :class="answerType(index)">
                  {{ answer }}
        </b-list-group-item>
            </b>
        </b-list-group>

    <b-button variant="primary" class="btn"
    @click="submitAnswer" :disabled="selectedIndex === null || answered">
        Submit
    </b-button>
    <b-button @click="next" variant="success" class="btn">Next</b-button>
  </b-jumbotron>
</div>
</template>

<script>
import _ from 'lodash'

export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function,
    },
    data() {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },
    computed: {
        answers(){
            let answers=[...this.currentQuestion.incorrect_answers]
            answers.push(this.currentQuestion.correct_answer)
            return answers
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler() {
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswers()
            }
        }

        // (){
        //     this.selectedIndex = null
        //     this.shuffleAnswers()
        // }
    },
    methods: {
        selectAnswer(index) {
            this.selectedIndex = index
        },
        submitAnswer() {
            let isCorrect = false

            if (this.selectedIndex === this.correctIndex){
                isCorrect = true
            }
            this.answered = true

            this.increment(isCorrect)
        },
        shuffleAnswers(){
            let answers=[...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            // lodash imported as an underscore
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        answerType(index) {
            let answerType = ''

            if (!this.answered && this.selectedIndex === index) {
                answerType = 'selected'
            }else if (this.answered && this.correctIndex === index) {
                answerType = 'correct'
            }else if (this.answered && this.selectedIndex === index && this.correctIndex !== index){
                answerType = 'incorrect'
            }

            return answerType
        }
    },
    // mounted() {
    //     console.log(this.currentQuestion)
    // },
}
</script>

<style scoped>
    .list-group{
        margin-bottom: 15px;
    }

    .list-group-item:hover{
        background: #eee;
        cursor: pointer;
    }

    .btn {
        margin: 0 5px;
    }

    .selected {
        background-color: lightblue;
        color: white;
    }

    .correct {
        background-color: lightgreen;
        color: white;
    }

    .incorrect {
        background-color: red;
        color: white;
    }
</style>