<template>
  <div v-if="json" class="mcq">
    <div v-if="nbPart === -1">
      <Result :score="score" :questions="totalQuestions"></Result>
      <div class="btn-group btn-group-toggle" role="group">
        <button class="btn btn-dark" @click="backHome">Home</button>
        <button class="btn btn-light" @click="restart">Restart</button>
      </div>
    </div>
    <div v-if="nbPart >= 0">
      <h2>{{ json.title }} </h2>
      <p v-if="json.instruction" >instructions : {{ json.instruction }}</p>
      <div class="card text-center">
        <div class="card-header text-muted">
          {{ json.mcq[nbPart].name }}
        </div>
        <b-progress height="2px" :value="passedQuestions" :max="nbQuestions"></b-progress>
        <div class="card-body">
          <h5 class="card-title">{{ json.mcq[nbPart].questions[nbQuestion].text }}</h5>
          <div v-if="json.mcq[nbPart].questions[nbQuestion].type === 'input'">
            <input class="form-control card-text" placeholder="Answer" v-model="input_answer" @keyup.enter="verify" id="inputAnswer" ref="inputAnswer" type="text" name="answer" required/>
          </div>
          <div v-if="json.mcq[nbPart].questions[nbQuestion].type === 'propal'">
            <div v-for="(propal, index) in json.mcq[nbPart].questions[nbQuestion].propal" :key="propal">
              <input class="radio card-text" type="radio" @keyup.enter="verify" :value="propal" id="propal" v-model="selected" @click="uncheck(propal)" />
              <label :for="propal" :ref="propal">{{ propal }}</label>
            </div>
          </div>
          <br>
          <div class="btn-group btn-group-toggle" role="group">
            <button class="btn btn-dark left" @click="backHome">Home</button>
            <button class="btn btn-primary" ref="verify_button" @click="verify">Verify</button>
            <button class="btn btn-primary" ref="next_button" disabled @click="next">Next</button>
          </div>
        </div>
        <div v-show="verifyButton" class="card-footer text-muted">
          <span style="color: green;">{{ answer }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import Result from './Result.vue';
  import 'bootstrap/dist/css/bootstrap.css';
  import 'bootstrap-vue/dist/bootstrap-vue.css';
  import Vue from 'vue'
  import BootstrapVue from 'bootstrap-vue';
  Vue.use(BootstrapVue);
  export default {
    name: 'Mcq',
    components: {
      Result,
    },
    props: {
      json: "",
      nbQuestions: 0,
      time: null,
    },
    data() {
      return {
        score: 0,
        nbPart: 0,
        nbQuestion: 0,
        input_answer: '',
        answer: "",
        list: ['one', 'two', 'three'],
        selected: "",
        previousSelected: "",
        totalQuestions: 0,
        progressQuestion: 0,
        passedQuestions: 0,
        verifyButton: false,
        goodAnswer: false
      }
    },
    methods: {
      backHome: function() {
        this.$emit('back-home');
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
            if (answers[i].toUpperCase() === this.input_answer.toUpperCase()) {
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
            if (answers[i].toUpperCase() === this.selected.toUpperCase()) {
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
        var answers = this.json.mcq[this.nbPart].questions[this.nbQuestion].answer;
        var type = this.json.mcq[this.nbPart].questions[this.nbQuestion].type;
        var verifyOK = false;
        if (type === 'input') verifyOK = this.verifyInputAnswer(answers);
        else verifyOK = this.verifyRadioAnswer(answers);
        if (verifyOK) {
          this.$refs.next_button.focus();
          this.verifyButton = true;
          this.$refs.verify_button.disabled = true;
          this.$refs.next_button.disabled = false;
          this.answer = this.json.mcq[this.nbPart].questions[this.nbQuestion].answer.toString();
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
        this.radio_answer
        this.verifyButton = false;
        this.$refs.verify_button.disabled = false;
        this.$refs.next_button.disabled = true;
        this.nbQuestion++;
        if (this.nbQuestion === this.json.mcq[this.nbPart].questions.length) {
          this.nbQuestion = 0;
          this.totalQuestions += this.json.mcq[this.nbPart].questions.length;
          this.nbPart++;
        }
        if (this.nbPart === this.json.mcq.length) {
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
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
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
