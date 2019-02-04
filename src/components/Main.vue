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
          </div>
          <div id="questions" v-else>
            <h4>Choosen category: {{selectedCategoryName}}</h4>
            <h4>Choosen id: {{selectedCategoryId}}</h4>
            <div>{{randomQuestionsArr}}</div>
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
                      :key="answer.id">{{i+1}}. {{answer.item}}
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
      correctAnswer: ''
    }
  },
  methods: {
    getCategory: function (id, name) {
      this.selectedCategoryId = id
      this.selectedCategoryName = name
    },
    resetCategory: function () {
      this.selectedCategoryName = ''
      this.selectedCategoryId = 0
      this.randomQuestionsArr = []
      this.actQuestion = ''
      this.possAnswers = []
      this.correctAnswer = ''
    },
    getRandomQuestions: function () {
      let arr = []
      while (arr.length < this.questionForPlay) {
        let r = Math.floor(Math.random() * this.questionPerCategory)
        if (arr.indexOf(r) === -1) arr.push(r)
      }
      this.randomQuestionsArr = arr
    },
    questionHandler: function () {
      let questionId = this.randomQuestionsArr.pop()
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
    }
  }
}
</script>
