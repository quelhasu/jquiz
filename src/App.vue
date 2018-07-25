<template>
  <div id="app">
    <nav class="navbar sticky-top navbar-dark bg-dark mb-5">
      <a class="navbar-brand" href="#">
        <img src="./assets/logo-white.png" width="30" height="30" class="d-inline-block align-top mr-2" alt="">JQuizz
      </a>
    </nav>
    <div id="content">
    <!-- <h1>Multiple choice questions JSON</h1> -->
    <!-- MAIN MENU -->
    <div id="selection" v-if="file === '' && !quizzCreation">
      <!-- QUIZZ CREATE/UPLOAD OPTION -->
      <div class="card text-center">
        <div class="card-header text-muted">
          Upload or create your quizz
        </div>
        <div class="flexbox">
          <div class="btn-group flex-item" role="group" aria-label="First group">
            <button @click="quizzCreation = true" style="border-radius: 0.25em 0 0 0.25em;" type="button" class="btn btn-outline-light full-width">Create quizz</button>
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
      <div id="available-quizz">
        <div class="card text-center">
          <div class="card-header text-muted">
            Available quizz
          </div>
          <div class="list-group">
            <a @click="loadFile('tober')" href="#" class="list-group-item list-group-item-action">Tober Quizz</a>
          </div>
        </div>
      </div>
    </div>
    <!-- QUIZZ -->
    <Quizz v-else :json="file" :nbQuestions="nbQuestions" v-on:back-home="displayHome"></Quizz>
    <!-- CREATION QUIZZ -->
    <QuizzCreation v-show="quizzCreation" v-on:back-home="displayHome"></QuizzCreation>
    </div>
  </div>
</template>

<script>
import Quizz from "./components/Quizz.vue";
import QuizzCreation from "./components/QuizzCreation.vue";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
import "./css/mobile.css";
import Vue from "vue";
import BootstrapVue from "bootstrap-vue";

Vue.use(BootstrapVue);

export default {
  name: "app",
  components: {
    Quizz,
    QuizzCreation
  },
  data() {
    return {
      file: "",
      nbQuestions: 0,
      quizzCreation: false
    };
  },
  mounted: function() {},
  methods: {
    displayHome: function() {
      this.file = "";
      this.quizzCreation = false;
    },
    loadFile: function(filePath) {
      switch (filePath) {
        case "tober":
          this.file = require("../public/json/tober.json");
          break;
      }
      this.browseFile(this.file);
    },
    browseFile: function(json) {
      var nb_questions = 0;
      json.quizz.forEach(function(item) {
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
  text-align: center;
  color: #2c3e50;
}
#content {
  margin-top: 60px;
  margin: 0 10vw;
}
#available-quizz {
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
