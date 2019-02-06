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
              <div class="col-md-3">
                <div class="progress-circle__container" v-if="userAnswers.length < questionForPlay">
                  <span class="progress-circle__percent">{{ countDownTimer }}</span>
                  <svg class="progress-circle" viewBox="0 0 106 106" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
                    <g id="Page-1" stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                      <g id="ProgressBar" transform="translate(-17.000000, -17.000000)">
                        <circle id="Oval" stroke="#949494" stroke-width="5" fill-rule="nonzero" cx="70" cy="70" r="50"></circle>
                        <path class="progress-circle__path" d="M70,120 C97.6142375,120 120,97.6142375 120,70 C120,42.3857625 97.6142375,20 70,20 C42.3857625,20 20,42.3857625 20,70 C20,97.6142375 42.3857625,120 70,120 Z" id="Oval-Copy" stroke-width="5" :stroke-dasharray="circle" fill-rule="nonzero" transform="translate(70.000000, 70.000000) rotate(-125.000000) translate(-70.000000, -70.000000) "></path>
                      </g>
                    </g>
                  </svg>
                </div>
              </div>
            </div>
            <div class="m-2">
              <button
                @click="questionHandler()"
                class="btn btn-info">
                {{questionBtnText}}
              </button>
            </div>
            <div class="m-2">
              <button
                @click="resetCategory()"
                class="btn btn-danger m-3">
                Reset Quiz
              </button>
            </div>
            <b-modal ref="myModalRef" hide-footer title="Your result">
              <div class="d-block text-center">
                <h3>{{resultTitle}}</h3>
                <h4><strong>{{resultPercentage}}%</strong></h4>
                <img class="img-fluid" :src="getIconPath(resultImgSrc)" alt="">
              </div>
              <b-btn class="mt-3" variant="outline-danger" @click="hideModal">Hide result</b-btn>
            </b-modal>
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
import resultJson from '../json/result.json'
export default {
  name: 'Main',
  data () {
    return {
      questionScience: scienceJson,
      questionHistory: historyJson,
      questionGastronomy: gastronomyJson,
      questionMovies: moviesJson,
      questionBooks: booksJson,
      resultCategories: resultJson,
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
      questionForPlayMin: 5,
      countDownTimer: 20,
      circlePercent: 0,
      countDownSteppes: 20,
      timerInterval: null,
      counterState: false,
      userCanAddTip: true,
      userAddedTip: false,
      resultTitle: '',
      resultImgSrc: '',
      resultPercentage: 0
    }
  },
  computed: {
    circle () {
      return ((this.circlePercent / 100) * 100 * Math.PI) + ',9999'
    },
    questionBtnText () {
      return this.userAnswers.length === this.questionForPlay ? 'Show my result!' : (this.randomQuestionsArr.length === this.questionForPlay ? 'Start Quiz!' : 'Next Question!')
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
    stopTimer: function () {
      this.counterState = false
      this.userCanAddTip = false
      this.userAddedTip = true
      clearInterval(this.timerInterval)
    },
    resetTimer: function () {
      clearInterval(this.timerInterval)
      this.counterState = false
      this.circlePercent = 0
      this.countDownTimer = this.countDownSteppes
    },
    startTimer: function () {
      this.countDown()
      this.timerInterval = setInterval(this.countDown, 1000)
    },
    countDown: function () {
      let n = this.countDownTimer
      if (!this.counterState) {
        this.counterState = true
      } else if (n > 0) {
        n = n - 1
        this.countDownTimer = n
        this.circlePercent = this.circlePercent + (100 / this.countDownSteppes)
      } else {
        this.resetTimer()
        this.userCanAddTip = false
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
      this.resetTimer()
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
      if (this.userAddedTip === false) {
        this.userAnswers.push('null')
      } else {
        if (this.userTip === undefined) {
          this.userAnswers.push('null')
        } else {
          if (this.userTip === this.correctAnswer) {
            this.userAnswers.push('true')
          } else {
            this.userAnswers.push('false')
          }
        }
      }
      this.userCanAddTip = true
      this.userAddedTip = false
    },
    questionHandler: function () {
      if (this.userAnswers.length !== this.questionForPlay) {
        /* get question */
        let questionId = this.randomQuestionsArr.pop()
        this.resetTimer()
        this.startTimer()
        if (this.randomQuestionsArr.length + 1 < this.questionForPlay) {
          this.saveUserTip()
        }
        this.userTip = undefined
        if (questionId !== undefined) {
          this.getQuestionByCategory(this.selectedCategoryId, questionId)
        } else {
          /* after last tip hide question and possAnswers */
          this.actQuestion = ''
          this.possAnswers = []
        }
      } else {
        this.calculateResult()
        this.showModal()
      }
    },
    calculateResult: function () {
      let countOfTrue = this.userAnswers.filter(x => x === 'true')
      this.resultPercentage = Math.round((countOfTrue.length / this.questionForPlay) * 100)
      let resultCategoriesJson = this.resultCategories
      switch (true) {
      case (this.resultPercentage === 100):
        this.resultTitle = resultCategoriesJson[0]['title']
        this.resultImgSrc = resultCategoriesJson[0]['gifs'][0]['src']
        break
      case (this.resultPercentage >= 80):
        this.resultTitle = resultCategoriesJson[1]['title']
        this.resultImgSrc = resultCategoriesJson[1]['gifs'][0]['src']
        break
      case (this.resultPercentage >= 60):
        this.resultTitle = resultCategoriesJson[2]['title']
        this.resultImgSrc = resultCategoriesJson[2]['gifs'][0]['src']
        break
      case (this.resultPercentage >= 40):
        this.resultTitle = resultCategoriesJson[3]['title']
        this.resultImgSrc = resultCategoriesJson[3]['gifs'][0]['src']
        break
      case (this.resultPercentage >= 20):
        this.resultTitle = resultCategoriesJson[4]['title']
        this.resultImgSrc = resultCategoriesJson[4]['gifs'][0]['src']
        break
      default:
        this.resultTitle = resultCategoriesJson[5]['title']
        this.resultImgSrc = resultCategoriesJson[5]['gifs'][0]['src']
        break
      }
    },
    showModal: function () {
      this.$refs.myModalRef.show()
    },
    hideModal: function () {
      this.$refs.myModalRef.hide()
    },
    getIconPath (iconName) {
      return iconName ? require(`@/assets/${iconName}`) : ''
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
      this.stopTimer()
    },
    setUserTip: function (tip) {
      if (this.userAddedTip === false) {
        if (this.userCanAddTip === false) {
          this.userTip = undefined
        } else {
          this.userTip = tip
        }
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

  .progress-circle {
    max-width:255px;
    max-height:255px;
    width:100%;
    transform: scaleX(-1) rotate(-55deg);

    &__percent {
      position:absolute;
      top:50%;
      left:50%;
      transform: translate(-50%,-50%);
    }

    &__container {
      display:inline-block;
      position:relative;
    }

    &__path {
      transition: 0.5s ease-in-out all;
      stroke: $green;
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
