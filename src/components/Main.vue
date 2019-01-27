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
              :class="['btn m-2 btn-' + category.color]"
              :key="category.id">
              {{ category.name }}
            </button>
            <h3 class="mt-3 mb-3">Quiz settings</h3>
            <div class="settings-board">
              <div class="form-group d-flex align-items-center">
                <label for="questionPerCategory" style="width: 250px" class="text-left">Possible question per category:</label>
                <input
                  id="questionPerCategory"
                  :type="number"
                  :min="10"
                  :max="20"
                  class="form-control ml-3"
                  style="width: 70px"
                  v-model="questionPerCategory"
                  :value="questionPerCategory"
                  readonly>
                <button class="btn btn-info ml-2" @click="questionPerCategoryInc()">+</button>
                <button class="btn btn-info ml-2" @click="questionPerCategoryDec()">-</button>
              </div>
              <div class="form-group d-flex align-items-center">
                <label for="questionPerCategory" style="width: 250px" class="text-left">Round per category:</label>
                <input
                  id="questionForPlay"
                  :type="number"
                  :min="5"
                  :max="10"
                  class="form-control ml-3"
                  style="width: 70px"
                  v-model="questionForPlay"
                  :value="questionForPlay"
                  readonly>
                <button class="btn btn-info ml-2" @click="questionForPlayInc()">+</button>
                <button class="btn btn-info ml-2" @click="questionForPlayDec()">-</button>
              </div>
            </div>

          </div>
          <div id="questions" v-else>
            <h2>Choosen category: {{selectedCategoryName}}</h2>
            <div class="question-progressbar d-flex mt-3 mb-3 justify-content-between align-items-center">
              <span class="question-progressbar-item"
              v-for="(question, i) in questionForPlay"
              :key="question.id"
              :class="userAnswers[i] !== undefined  ? (userAnswers[i] === 'null' ? 'user-tip-skip' : (userAnswers[i] === 'true' ? 'user-tip-true' : 'user-tip-false')) : ''"
              ></span>
              {{userAnswers[i]}}
            </div>
            <div class="question-block">
              <h2 v-html="actQuestion"></h2>
              <div>{{randomQuestionsArr}}</div>
              <div class="text-center">
                <ul style="width: 300px; display:inline-block;" class="mt-5">
                  <li
                    disabled
                    class="text-left mb-3 d-flex align-items-center"
                    :class="userTip === answer.id ? (userTip === correctAnswer ? 'correct' : 'wrong') : ''"
                    v-for="(answer, i) in possAnswers"
                    :key="answer.id">
                    <span class="question-checkbox mr-3" @click="getUserTip(answer.id)"></span>{{i+1}}. {{answer.item}}
                  </li>
                </ul>
              </div>
            </div>
            <div v-if="randomQuestionsArr.length === questionForPlay">
              <button
                @click="showQuestion()"
                class="btn btn-info">
                Start Quiz!
              </button>
            </div>
            <div v-else>
              <button
                @click="showQuestion()"
                class="btn btn-info">
                Next Question!
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
      questionPerCategoryMax: 20,
      questionPerCategoryMin: 10,
      questionForPlay: 5,
      questionForPlayMax: 10,
      questionForPlayMin: 5,
      actQuestion: '',
      possAnswers: [],
      userAnswers: [],
      correctAnswer: '',
      randomQuestionsArr: [],
      selectedCategoryId: 0,
      selectedCategoryName: ''
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
      this.selectedCategoryName = name
      this.selectedCategoryId = id
    },
    resetCategory: function () {
      this.selectedCategoryName = ''
      this.selectedCategoryId = 0
      this.actQuestion = ''
      this.possAnswers = []
      this.userAnswers = []
    },
    saveUserTip: function () {
      if (this.userTip === undefined) {
        this.userAnswers.push('null')
      } else {
        if (this.userTip === this.correctAnswer) {
          this.userAnswers.push('true')
        } else {
          this.userAnswers.push('false')
        }
      }
    },
    showQuestion: function () {
      let question = this.randomQuestionsArr.pop()
      if (this.randomQuestionsArr.length + 1 < this.questionForPlay) {
        this.saveUserTip()
      }
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
      this.randomQuestionsArr = arr
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  @import '../scss/variables';

  .user-tip {
    &-skip {
      background: $gray!important;
      color: $white;
    }
    &-true {
      background: $green!important;
      color: $white;
    }
    &-false {
      background: $red!important;
      color: $white;
    }
  }

  .settings-board {
    width: 400px;
    margin: 0 auto;
  }

  .question {
    &-checkbox {
      width: 50px;
      height: 50px;
      background: $gray-light;
      border-radius: 10px;
      display: inline-block;
    }
    &-progressbar {
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
    }
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
