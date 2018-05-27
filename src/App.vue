<template>
  <div id="app">
    <h1>Multiple choice questions JSON</h1>
    <div id="selection" v-if="file === ''">
      <div class="card text-center">
        <div class="card-header text-muted">
          Upload your quizz
        </div>
          <div class="input-group">
            <div class="custom-file">
              <input id="inputGroupFile01" name='file' :ref="file" type="file" @change="loadJson($event)">
              <label class="custom-file-label" for="inputGroupFile01">Choose file</label>
            </div>
          </div>
      </div>
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
    <Quizz v-else :json="file" :nbQuestions="nbQuestions" v-on:back-home="displayHome"></Quizz>
  </div>
</template>

<script>
  import Quizz from './components/Quizz.vue';
  import 'bootstrap/dist/css/bootstrap.css'
  import 'bootstrap-vue/dist/bootstrap-vue.css'
  import Vue from 'vue'
  import BootstrapVue from 'bootstrap-vue';
  Vue.use(BootstrapVue);
  export default {
    name: 'app',
    components: {
      Quizz
    },
    data() {
      return {
        file: "",
        nbQuestions: 0,
      }
    },
    mounted: function() {
      // console.log(this.Json.Quizz.length + " -- " + this.nbPart);
    },
    methods: {
      displayHome: function(){
        this.file = "";
      },
      loadFile: function(filePath){
        switch(filePath){
          case 'tober': this.file = require('../public/json/tober.json');
        }
      },
      browseFile: function(json) {
        var nb_questions = 0;
        json.mcq.forEach(function(item) {
          nb_questions += item.questions.length;
        });
        this.nbQuestions = nb_questions;
      },
      loadJson: function(event) {
        var file = event.target.files[0]
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
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    margin: 60px 10vw 0 10vw;
  }
  #available-quizz {
    padding-top: 50px;
  }
  /* .card {
          border-radius: 0 0 0.25em 0.25em;
        } */
  .progress {
    border-radius: 0;
  }
</style>
