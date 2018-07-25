<template>
  <div v-if="json" class="quizz">
    <div v-if="nbPart === -1">
      <Result :score="score" :questions="totalQuestions"></Result>
      <div class="btn-group btn-group-toggle" role="group">
        <button class="btn btn-responsive btn-dark" @click="backHome">Home</button>
        <button class="btn btn-responsive btn-light" @click="restart">Restart</button>
      </div>
    </div>
    <div v-if="nbPart >= 0">
      <p v-if="json.instructions" >instructions : {{ json.instructions }}</p>
      <div class="card text-center">
        <div class="card-header">
          <button class="btn btn-responsive btn-link btn-home ml-2" @click="backHome">Home</button>
          <h4 class="card-title">{{ json.title }} </h4>
          <h5 class="card-subtitle text-muted">{{ json.quizz[nbPart].name }}</h5>
        </div>
        <b-progress height="10px" :value="passedQuestions" :max="nbQuestions"></b-progress>
        <div class="card-body">
          <div v-if="json.quizz[nbPart].questions[nbQuestion].image">
            <img class="card-img" :src="json.quizz[nbPart].questions[nbQuestion].image"/>
            <br>
          </div>
          <h5 class="card-title">{{ json.quizz[nbPart].questions[nbQuestion].text }}</h5>
          <div v-if="json.quizz[nbPart].questions[nbQuestion].type === 'input'">
            <input class="form-control card-text" placeholder="Answer" v-model="input_answer" @keyup.enter="verify" id="inputAnswer" ref="inputAnswer" type="text" name="answer" required/>
          </div>
          <div v-if="json.quizz[nbPart].questions[nbQuestion].type === 'radio'">
            <div v-for="(radio, index) in json.quizz[nbPart].questions[nbQuestion].propal" :key="radio">
              <input class="radio card-text" type="radio" @keyup.enter="verify" :value="radio" id="radio" v-model="selected" @click="uncheck(radio)" />
              <label :for="radio" :ref="radio">{{ radio }}</label>
            </div>
          </div>
          <br>
          <div class="btn-group btn-group-toggle" role="group">
            <button class="btn btn-responsive btn-primary" ref="verify_button" @click="verify">Verify</button>
            <button class="btn btn-responsive btn-primary" ref="next_button" disabled @click="next">Next</button>
          </div>
        </div>
        <div v-show="verifyButton" class="card-footer text-muted">
          <span style="color: green;">{{ answers }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Result from "./Result.vue";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
import Vue from "vue";
import BootstrapVue from "bootstrap-vue";
Vue.use(BootstrapVue);
export default {
  name: "Quizz",
  components: {
    Result
  },
  props: {
    json: "",
    nbQuestions: 0,
    time: null
  },
  data() {
    return {
      score: 0,
      nbPart: 0,
      nbQuestion: 0,
      input_answer: "",
      answers: "",
      list: ["one", "two", "three"],
      selected: "",
      previousSelected: "",
      totalQuestions: 0,
      progressQuestion: 0,
      passedQuestions: 0,
      verifyButton: false,
      goodAnswer: false
    };
  },
  methods: {
    backHome: function() {
      this.$emit("back-home");
    },
    uncheck: function(val) {
      if (val === this.previousSelected) {
        this.selected = false;
      }
      this.previousSelected = this.selected;
    },
    uncheckAll: function() {
      this.selected = false;
    },
    verifyInputAnswer: function(answers) {
      var Ok = false;
      if (this.input_answer) {
        Ok = true;
        for (var i in answers) {
          if (
            answers[i].replace(/\s/g, "").toUpperCase() ===
            this.input_answer.replace(/\s/g, "").toUpperCase()
          ) {
            this.goodAnswer = true;
            break;
          }
        }
        if (this.goodAnswer) {
          this.$refs.inputAnswer.style.border = "green 1px solid";
          this.$refs.inputAnswer.classList.add("is-valid");
        } else {
          this.$refs.inputAnswer.style.border = "red 1px solid";
          this.$refs.inputAnswer.classList.add("is-invalid");
        }
      } else {
        alert("Write an answer!");
      }
      return Ok;
    },
    verifyRadioAnswer: function(answers) {
      var Ok = false;
      if (this.selected) {
        Ok = true;
        for (var i in answers) {
          if (
            answers[i].replace(/\s/g, "").toUpperCase() ===
            this.selected.replace(/\s/g, "").toUpperCase()
          ) {
            this.goodAnswer = true;
            break;
          }
        }
        if (this.goodAnswer) this.$refs[this.selected][0].style.color = "green";
        else this.$refs[this.selected][0].style.color = "red";
      } else {
        alert("Choose an answer!");
      }
      return Ok;
    },
    verify: function() {
      var answers = this.json.quizz[this.nbPart].questions[this.nbQuestion]
        .answers;
      var type = this.json.quizz[this.nbPart].questions[this.nbQuestion].type;
      var verifyOK = false;
      if (type === "input") verifyOK = this.verifyInputAnswer(answers);
      else verifyOK = this.verifyRadioAnswer(answers);
      if (verifyOK) {
        this.$refs.next_button.focus();
        this.verifyButton = true;
        this.$refs.verify_button.disabled = true;
        this.$refs.next_button.disabled = false;
        this.answers = this.json.quizz[this.nbPart].questions[
          this.nbQuestion
        ].answers.toString();
      }
    },
    next: function() {
      if (this.goodAnswer) {
        this.score++;
        this.goodAnswer = false;
      }
      this.passedQuestions++;
      this.uncheckAll();
      this.input_answer = "";
      this.answer = "";
      this.radio_answer;
      this.verifyButton = false;
      this.$refs.verify_button.disabled = false;
      this.$refs.next_button.disabled = true;
      this.nbQuestion++;
      if (this.nbQuestion === this.json.quizz[this.nbPart].questions.length) {
        this.nbQuestion = 0;
        this.totalQuestions += this.json.quizz[this.nbPart].questions.length;
        this.nbPart++;
      }
      if (this.nbPart === this.json.quizz.length) {
        this.nbPart = -1;
      }
    },
    restart: function() {
      this.nbQuestion = 0;
      this.nbPart = 0;
      this.passedQuestions = 0;
      this.goodAnswer = false;
      this.score = 0;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.card-title{
  margin-top: 10px;
}
.card-image{
  width: 100%
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
