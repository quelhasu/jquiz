<template>
  <div id="app">
    <nav class="navbar sticky-top navbar-dark bg-dark mb-5">
      <a class="navbar-brand" href="#">
        <img src="./assets/logo-white.png" width="30" height="30" class="d-inline-block align-top mr-2" alt="">JQuiz
      </a>
    </nav>
    <div id="content">
    <!-- <h1>Multiple choice questions JSON</h1> -->
    <!-- MAIN MENU -->
    <div id="selection" v-if="file === '' && !quizCreation">
      <div class="jumbotron jumbotron-fluid">
        <div class="container">
          <h1 class="display-4">JQuiz</h1>
          <p class="lead">Need to practice for an exam or just want to test your friends or whatever..? Do it thanks to this tool which allows you to consult, upload and create your own quiz. </p>
          <hr class="my-4">
          <p>Create your own quiz with different types of questions including images or videos. 
            <br>You can also upload an existing quiz in json format that you have been given or previously saved.
            <br>For more information on how using this tool, visit the GitHub page.
          </p>
          <p class="lead">
            <a target="_blank" class="btn btn-primary btn-lg" href="https://github.com/quelhasu/jquiz" role="button">Learn more</a>
          </p>
        </div>
      </div>
      <!-- QUIZZ CREATE/UPLOAD OPTION -->
      <div class="card text-center">
        <div class="card-header text-muted">
          Upload or create your quiz
        </div>
        <div class="flexbox">
          <div class="btn-group flex-item" role="group" aria-label="First group">
            <button @click="quizCreation = true" style="border-radius: 0.25em 0 0 0.25em;" type="button" class="btn btn-outline-light full-width">Create quiz</button>
          </div>
          <div class="input-group flex-item">
            <div class="custom-file">
              <input id=inputGroupFile01 name='file' :ref="file" type="file" style="display: none;" @change="loadJson($event)">
              <label style="padding-right: 25%; border-radius: 0 0.25em 0.25em 0;" class="custom-file-label" for="inputGroupFile01">Choose file</label>
            </div>
          </div>
        </div>
      </div>
      <!-- AVAILABLE QUIZZ -->
      <div id="available-quiz">
        <div class="card text-center">
          <div class="card-header text-muted">
            Available quiz
          </div>
          <div class="list-group">
            <a @click="loadFile('example')" href="#" class="list-group-item list-group-item-action">Quiz example</a>
          </div>
        </div>
      </div>
    </div>
    <!-- QUIZZ -->
    <Quiz v-else :json="file" :nbQuestions="nbQuestions" v-on:back-home="displayHome"></Quiz>
    <!-- CREATION QUIZZ -->
    <QuizCreation v-show="quizCreation" v-on:back-home="displayHome"></QuizCreation>
    </div>
  </div>
</template>

<script>
import Quiz from "./components/Quiz.vue";
import QuizCreation from "./components/QuizCreation.vue";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
import "./css/mobile.css";
import Vue from "vue";
import BootstrapVue from "bootstrap-vue";

Vue.use(BootstrapVue);

export default {
  name: "app",
  components: {
    Quiz,
    QuizCreation
  },
  data() {
    return {
      file: "",
      nbQuestions: 0,
      quizCreation: false
    };
  },
  mounted: function() {},
  methods: {
    displayHome: function() {
      this.file = "";
      this.quizCreation = false;
    },
    loadFile: function(filePath) {
      switch (filePath) {
        case "example":
          this.file = require("../public/json/example.json");
          break;
      }
      this.browseFile(this.file);
    },
    browseFile: function(json) {
      var nb_questions = 0;
      json.quiz.forEach(function(item) {
        nb_questions += item.questions.length;
      });
      this.nbQuestions = nb_questions;
    },
    loadJson: function(event) {
      var file = event.target.files[0];
      var reader = new FileReader();
      var vm = this;
      if (file.type == "application/json") {
        reader.onload = function(e) {
          vm.file = JSON.parse(e.target.result);
          vm.browseFile(vm.file);
        };
        reader.readAsText(file);
      } else {
        alert("Pleaser select a .json file");
      }
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
}
#content {
  margin-top: 60px;
  margin: 0 10vw;
}
#available-quiz {
  padding-top: 50px;
}
.progress {
  border-radius: 0;
}
.flex-item {
  flex: 1 0 0;
  width: 100%;
}
.full-width {
  width: 100%;
}
.btn-outline-light {
  border: 1px solid #ced4da;
  color: #495057;
}
.btn-home {
  position: absolute;
  left: 0;
}
.flexbox {
  display: flex;
}
</style>
