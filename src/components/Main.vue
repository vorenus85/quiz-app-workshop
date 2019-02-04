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
              @click="getCategory(category.id,category.name)"
              :key="category.id">
              {{ category.name }}
            </button>
          </div>
          <div id="questions" v-else>
            <h4>Choosen category: {{selectedCategoryName}}</h4>
            <h4>Choosen id: {{selectedCategoryId}}</h4>
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
      selectedCategoryId: 0
    }
  },
  methods: {
    getCategory: function (id, name) {
      console.log(id, name)
      this.selectedCategoryId = id
      this.selectedCategoryName = name
    },
    resetCategory: function () {
      this.selectedCategoryName = ''
      this.selectedCategoryId = 0
    }
  }
}
</script>
