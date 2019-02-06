<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-12 mt-5">
          <div id="choose-category" v-if="selectedCategoryId === 0">
            <h1>Choose category</h1>
            <button
              v-for="category in categories"
              :class="['btn m-2 btn-' + category.color]"
              @click="getCategory(category.id,category.name),getRandomQuestions()"
              :key="category.id">
              {{ category.name }}
            </button>
            <h3 class="mt-3 mb-3">Quiz settings</h3>
            <div class="settings-board">
              <div class="form-group d-flex align-items-center">
                <label for="questionPerCategory" style="width: 250px" class="text-left">Possible question per category:</label>
                <input
                  id="questionPerCategory"
                  type="number"
                  min="10"
                  max="20"
                  class="form-control ml-3"
                  style="width: 70px"
                  v-model="questionPerCategory"
                  readonly>
                <button class="btn btn-info ml-2" @click="questionPerCategoryInc()">+</button>
                <button class="btn btn-info ml-2" @click="questionPerCategoryDec()">-</button>
              </div>
              <div class="form-group d-flex align-items-center">
                <label for="questionPerCategory" style="width: 250px" class="text-left">Round per category:</label>
                <input
                  id="questionForPlay"
                  type="number"
                  min="5"
                  max="10"
                  class="form-control ml-3"
                  style="width: 70px"
                  v-model="questionForPlay"
                  readonly>
                <button class="btn btn-info ml-2" @click="questionForPlayInc()">+</button>
                <button class="btn btn-info ml-2" @click="questionForPlayDec()">-</button>
              </div>
              <h5>Possible question per category: <strong>{{questionPerCategory}}</strong></h5>
              <h5>Round per category: <strong>{{questionForPlay}}</strong></h5>
            </div>
          </div>
          <div id="questions" v-else>
            <h4>Choosen category: {{selectedCategoryName}}</h4>
            <h4>Choosen id: {{selectedCategoryId}}</h4>
            <div>{{randomQuestionsArr}}</div>
            <div class="question-progressbar d-flex mt-3 mb-3 justify-content-between align-items-center">
              <span class="question-progressbar-item"
                    v-for="(question, index) in questionForPlay"
                    :class="progressBarValidation(userAnswers[index])"
                    :key="question.id"
              ></span>
            </div>
            <div class="row">
              <div class="col-md-12">
                <h2 v-html="actQuestion"></h2>
              </div>
              <div class="question-block col-md-9">
                <div class="text-center">
                  <ul style="width: 300px; display:inline-block;" class="mt-5">
                    <li
                      class="text-left mb-3 d-flex align-items-center"
                      v-for="(answer, i) in possAnswers"
                      :class="answerValidation(answer.id)"
                      :key="answer.id">
                      <span class="question-checkbox mr-3" @click="getUserTip(answer.id)"></span>{{i+1}}. {{answer.item}}
                    </li>
                  </ul>
                </div>
              </div>
            </div>
            <div class="m-2">
              <button
                @click="questionHandler()"
                class="btn btn-info">
                Show question
              </button>
            </div>
            <div class="m-2">
              <button
                @click="resetCategory()"
                class="btn btn-danger m-3">
                Reset Quiz
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import booksJson from '../json/books.json'
import gastronomyJson from '../json/gastronomy.json'
import historyJson from '../json/history.json'
import moviesJson from '../json/movies.json'
import scienceJson from '../json/science.json'
export default {
  name: 'Main',
  data () {
    return {
      questionScience: scienceJson,
      questionHistory: historyJson,
      questionGastronomy: gastronomyJson,
      questionMovies: moviesJson,
      questionBooks: booksJson,
      categories: [
        {
          id: 1,
          name: 'Science and technology',
          color: 'primary'
        },
        {
          id: 2,
          name: 'History',
          color: 'info'
        },
        {
          id: 3,
          name: 'Gastronomy',
          color: 'warning'
        },
        {
          id: 4,
          name: 'Movies',
          color: 'success'
        },
        {
          id: 5,
          name: 'Books',
          color: 'danger'
        }
      ],
      selectedCategoryName: '',
      selectedCategoryId: 0,
      questionPerCategory: 10,
      questionForPlay: 5,
      randomQuestionsArr: [],
      actQuestion: '',
      possAnswers: [],
      correctAnswer: '',
      userTip: undefined,
      userAnswers: [],
      questionPerCategoryMax: 20,
      questionPerCategoryMin: 10,
      questionForPlayMax: 10,
      questionForPlayMin: 5
    }
  },
  methods: {
    questionPerCategoryInc: function () {
      if (this.questionPerCategory <= this.questionPerCategoryMax - 1 && this.questionPerCategory >= this.questionPerCategoryMin - 1) {
        this.questionPerCategory++
      }
    },
    questionPerCategoryDec: function () {
      if (this.questionPerCategory - 1 <= this.questionPerCategoryMax && this.questionPerCategory - 1 >= this.questionPerCategoryMin) {
        this.questionPerCategory--
      }
    },
    questionForPlayInc: function () {
      if (this.questionForPlay <= this.questionForPlayMax - 1 && this.questionForPlay >= this.questionForPlayMin - 1) {
        this.questionForPlay++
      }
    },
    questionForPlayDec: function () {
      if (this.questionForPlay - 1 <= this.questionForPlayMax && this.questionForPlay - 1 >= this.questionForPlayMin) {
        this.questionForPlay--
      }
    },
    getCategory: function (id, name) {
      this.selectedCategoryId = id
      this.selectedCategoryName = name
    },
    resetCategory: function () {
      this.selectedCategoryName = ''
      this.selectedCategoryId = 0
      this.userAnswers = []
      this.randomQuestionsArr = []
      this.actQuestion = ''
      this.possAnswers = []
      this.correctAnswer = ''
      this.questionPerCategory = 10
      this.questionForPlay = 5
    },
    getRandomQuestions: function () {
      let arr = []
      while (arr.length < this.questionForPlay) {
        let r = Math.floor(Math.random() * this.questionPerCategory)
        if (arr.indexOf(r) === -1) arr.push(r)
      }
      this.randomQuestionsArr = arr
    },
    answerValidation: function (answerId) {
      return this.userTip === answerId ? (this.userTip === this.correctAnswer ? 'correct' : 'wrong') : ''
    },
    progressBarValidation: function (userAnswer) {
      return userAnswer !== undefined ? (userAnswer === 'null' ? 'user-tip-skip' : (userAnswer === 'true' ? 'user-tip-true' : 'user-tip-false')) : ''
    },
    saveUserTip: function () {
      if (this.userTip === undefined) { // if user not choose any tip
        this.userAnswers.push('null')
      } else {
        if (this.userTip === this.correctAnswer) {
          this.userAnswers.push('true')
        } else {
          this.userAnswers.push('false')
        }
      }
    },
    questionHandler: function () {
      let questionId = this.randomQuestionsArr.pop() // next question
      if (this.randomQuestionsArr.length + 1 < this.questionForPlay) {
        this.saveUserTip()
      }
      this.userTip = undefined // reset userTip
      if (questionId !== undefined) {
        this.getQuestionByCategory(this.selectedCategoryId, questionId)
      } else {
        /* after last tip hide question and possAnswers */
        this.actQuestion = ''
        this.possAnswers = []
      }
    },
    getQuestionByCategory: function (categoryId, questionId) {
      let questionJson = []
      this.actQuestion = ''
      this.possAnswers = []
      switch (categoryId) {
      case 1: {
        questionJson = this.questionScience
        break
      }
      case 2: {
        questionJson = this.questionHistory
        break
      }
      case 3: {
        questionJson = this.questionGastronomy
        break
      }
      case 4: {
        questionJson = this.questionMovies
        break
      }
      case 5: {
        questionJson = this.questionBooks
        break
      }
      default: {
        questionJson = this.questionScience
        break
      }
      }
      this.actQuestion = questionJson[questionId]['question']
      for (let i = 0; i < 4; i++) {
        this.possAnswers.push({
          item: questionJson[questionId]['answers'][i]['item'],
          id: questionJson[questionId]['answers'][i]['id']})
      }
      this.correctAnswer = questionJson[questionId]['correct']
    },
    getUserTip: function (answerId) {
      this.setUserTip(answerId)
    },
    setUserTip: function (tip) {
      if (this.userTip === undefined) { // to prevent secong tip chance
        this.userTip = tip
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  @import '../scss/variables';
  .question {
    &-checkbox {
      flex: 0 0 50px;
      height: 50px;
      background: $gray-light;
      border-radius: 10px;
      display: inline-block;
      cursor: pointer;
    }
  }

  .settings-board {
    width: 400px;
    margin: 0 auto;
  }

  .wrong .question-checkbox{
    background: $red;
  }

  .correct .question-checkbox{
    background: $green;
  }

  .question-progressbar {
    position: relative;
    width: 500px;
    margin: 0 auto;

    &-item {
      width: 20px;
      height: 20px;
      border-radius: 100%;
      background: $gray-lighter;
      box-shadow: 0 0 10px 0 rgba(0,0,0,.3);
      position: relative;
      z-index: 2;
      font-size: 12px;
    }

    &:before {
      content: '';
      background: $gray-light;
      width: 100%;
      height: 1px;
      display: inline-block;
      position: absolute;
      z-index: 1;
    }

    .user-tip {
      &-skip {
        background: $gray;
        color: $white;
      }
      &-true {
        background: $green;
        color: $white;
      }
      &-false {
        background: $red;
        color: $white;
      }
    }
  }
</style>
