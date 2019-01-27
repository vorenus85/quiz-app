<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-md-12 mt-5">
          <div id="choose-category" v-if="selectedCategoryId === 0">
            <h1> Choose category</h1>
            <button
              v-for="category in categories"
              @click="getCategory(category.id,category.name),getRandomQuestions()"
              :class="['btn ml-2 mr-2 btn-' + category.color]"
              :key="category.id">
              {{ category.name }}
            </button>
          </div>
          <div id="questions" v-else>
            <h2>Choosen category id: {{selectedCategoryId}}</h2>
            <div>Choosen category name: {{selectedCategoryName}}</div>
            <div>{{randomQuestion}}</div>
            <div class="question-block">
              <h2 v-html="actQuestion"></h2>
              <div class="text-center">
                <ul style="width: 300px; display:inline-block;" class="mt-5">
                  <li
                    class="text-left mb-3 d-flex align-items-center"
                    v-bind:class="userTip === answer.id ? (userTip === correctAnswer ? 'correct' : 'wrong') : ''"
                    v-for="(answer, i) in possAnswers"
                    :key="answer.id">
                    <span class="question-checkbox mr-3" @click="getUserTip(answer.id)"></span>{{i+1}}. {{answer.item}}
                  </li>
                </ul>
              </div>
            </div>
            <div>
              <button
                @click="showQuestion()"
                class="btn btn-info">
                Show Question
              </button>
            </div>
            <div>
              <button
                @click="resetCategory()"
                class="btn btn-link">
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
  // state
  data () {
    return {
      questionScience: scienceJson,
      questionHistory: historyJson,
      questionGastronomy: gastronomyJson,
      questionMovies: moviesJson,
      questionBooks: booksJson,
      userTip: undefined,
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
      questionPerCategory: 10,
      questionForPlay: 5,
      actQuestion: '',
      possAnswers: [],
      correctAnswer: '',
      randomQuestion: [],
      selectedCategoryId: 0,
      selectedCategoryName: ''
    }
  },
  methods: {
    getCategory: function (id, name) {
      this.selectedCategoryName = name
      this.selectedCategoryId = id
    },
    resetCategory: function () {
      this.selectedCategoryName = ''
      this.selectedCategoryId = 0
      this.actQuestion = ''
      this.possAnswers = []
    },
    showQuestion: function () {
      let question = this.randomQuestion.pop()
      this.userTip = undefined
      this.getQuestionByCategory(this.selectedCategoryId, question)
    },
    getUserTip: function (answerId) {
      this.setUserTip(answerId)
    },
    setUserTip: function (tip) {
      if (this.userTip === undefined) {
        this.userTip = tip
      } else {
        this.userTip = this.userTip
      }
    },
    getQuestionByCategory: function (categoryId, questionId) {
      let questionJson = []
      this.actQuestion = ''
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
      this.possAnswers = []
      this.actQuestion = questionJson[questionId]['question']
      for (let i = 0; i < 4; i++) {
        this.possAnswers.push({
          item: questionJson[questionId]['answers'][i]['item'],
          id: questionJson[questionId]['answers'][i]['id']})
      }
      this.correctAnswer = questionJson[questionId]['correct']
    },
    getRandomQuestions: function () {
      let arr = []
      while (arr.length < this.questionForPlay) {
        let r = Math.floor(Math.random() * this.questionPerCategory)
        if (arr.indexOf(r) === -1) arr.push(r)
      }
      this.randomQuestion = arr
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  @import '../scss/variables';

  .question-checkbox {
    width: 50px;
    height: 50px;
    background: $gray-light;
    border-radius: 10px;
    display: inline-block;
  }

  .wrong .question-checkbox{
    background: $red;
  }

  .correct .question-checkbox{
    background: $green;
  }

  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
    li {
      margin: 0 10px;
    }
  }

  a {
    color: #42b983;
  }
</style>
