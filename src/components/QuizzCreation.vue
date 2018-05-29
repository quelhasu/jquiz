<template>
  <div id="quizz-creation">
    <!-- TITLE / INSTRUCTION FRAME -->
    <div v-if="next===false">
      <div class="card text-center">
        <div class="card-header">
          <button class="btn btn-responsive btn-link btn-finish mr-2" @click="nexButton">Next</button>
          <button class="btn btn-responsive btn-link btn-home ml-2" @click="backHome">Home</button>
          <h5 class="card-title text-muted mt-2">Name your quizz</h5>
        </div>
        <div class="card-body">
          <input v-model="json.title" class="form-control card-text mb-3" placeholder="Quizz title" @keyup.enter="nexButton" />
          <textarea v-model="json.instruction" class="form-control card-text" placeholder="Instructions" @keyup.enter="nexButton" />
        </div>
      </div>
    </div>
    <!-- QUESTION / PART FRAME -->
    <div v-else>
      <!-- ADD QUESTION CARD -->
      <div class="card text-center">
        <div class="card-header">
          <button class="btn btn-responsive btn-outline-success btn-finish mr-2" @click="finish">Finish</button>
          <button class="btn btn-responsive btn-link btn-home ml-2" @click="backHome">Home</button>
          <h5 class="">{{json.title}} quizz</h5>
          <h6 class="card-subtitle text-muted">Select type and add question/answers</h6>
        </div>
        <div class="card-body">
          <input v-model="part.name" class="form-control cart-tex mb-3" placeholder="Part name" ref="inputPart" />
          <div class="form-row mb-4">
            <div class="col-3">
              <select v-model="typeQuestion" class="custom-select mr-sm-2" id="inlineFormCustomSelect">
                          <option value="" disabled selected>Choose...</option>
                          <option value="input">Input</option>
                          <option value="radio">Radio</option>
                        </select>
            </div>
            <div class="col">
              <input v-model="question" class="form-control card-text" placeholder="Question" id="inputQuestion" ref="inputQuestion" type="text" name="question" disabled/>
            </div>
          </div>
          <!-- QUESTION INPUT -->
          <div v-show="typeQuestion === 'input'">
            <h6 class="text-muted">Seperate answers with ','</h6>
            <input v-model="inputAnswers" class="form-control card-text" placeholder="Answers" id="inputAnswers" ref="answers" type="text" name="answers" />
          </div>
          <!-- QUESTION RADIO -->
          <div v-show="typeQuestion === 'radio'">
            <h6 class="text-muted text-left">Seperate proposals with ','</h6>
            <input v-model="inputProposals" class="form-control card-text mb-3" placeholder="Proposals" id="inputProposals" ref="proposals" type="text" name="answers" />
            <h6 class="text-muted text-left">Seperate answers with ','</h6>
            <input v-model="inputAnswers" class="form-control card-text" placeholder="Answers" id="inputProposals" ref="proposals" type="text" name="answers" />
          </div>
          <br>
          <div class="btn-group btn-group-toggle" role="group">
            <button class="btn btn-responsive btn-outline-dark" ref="addButton" @click="addQuestion">Add Question</button>
            <button class="btn btn-responsive btn-outline-dark" ref="addPart" @click="addPart">Add Part</button>
          </div>
        </div>
        <div class="card-footer text-muted">
          <span class="badge badge-info">part : {{nbPart}}</span>
          <span class="badge badge-info">questions : {{nbQuestions}}</span>
        </div>
      </div>
      <br>
      <!-- JSON PREVIEW CARD -->
      <div class="card">
        <div class="card-header" header-tag="header" role="tabt">
          <h5 href="#" v-b-toggle.json-preview variant="info">JSON Preview</h5>
        </div>
        <b-collapse id="json-preview" accordion="my-accordion" role="tabpanel">
        <div class="card-body not-center">
          <tree-view :data="json" :options="{maxDepth: 3, rootObjectKey:'Quizz object'}"></tree-view>
        </div>
        </b-collapse>
      </div>
    </div>
  </div>
</template>

<script>
  import 'bootstrap/dist/css/bootstrap.css';
  import 'bootstrap-vue/dist/bootstrap-vue.css';
  import Vue from 'vue'
  import BootstrapVue from 'bootstrap-vue';
  import TreeView from "vue-json-tree-view"
  Vue.use(TreeView)
  Vue.use(BootstrapVue);
  export default {
    name: 'QuizzCreation',
    components: {},
    props: {},
    data() {
      return {
        json: {
          title: "",
          instruction: null,
          quizz: []
        },
        part: {
          name: null,
          questions: []
        },
        typeQuestion: null,
        inputTitle: "",
        question: null,
        inputAnswers: null,
        inputProposals: null,
        next: false,
        nbQuestions: 0,
        nbPart: 0,
        
      }
    },
    watch: {
      typeQuestion(val) {
        this.$refs.inputQuestion.disabled = false;
        this.question = "";
        this.answers = "";
      },
    },
    methods: {
      // Set data to their default value
      resetDate: function() {
        this.json = {
          title: "",
          instruction: null,
          quizz: []
        };
        this.part = {
          name: null,
          questions: []
        };
        this.next = false;
        this.typeQuestion = null;
        this.nbQuestions = 0;
        this.nbPart = 0;
       if(this.$refs.inputPart) this.$refs.inputPart.disabled = false;
      },
      // Donwload the json file when quizz is finished
      finish: function() {
        // this.download = "data:text/json;charset=utf-8,"+encodeURIComponent(JSON.stringify(this.json));
        let dataStr = JSON.stringify(this.json);
        let dataUri = 'data:application/json;charset=utf-8,' + encodeURIComponent(dataStr);
        let exportFileDefaultName = 'data.json';
        let linkElement = document.createElement('a');
        linkElement.setAttribute('href', dataUri);
        linkElement.setAttribute('download', exportFileDefaultName);
        linkElement.click();
        this.backHome();
      },
      // Add part to the JSON object
      addPart: function() {
        if (this.part.name != null) {
          this.$refs.inputPart.disabled = false;
          this.json.quizz.push(this.part);
          this.part = {
            name: null,
            questions: []
          };
          this.nbPart++;
        } else alert("Please name your part!");
      },
      // Change content of the frame when title/instructions have been written
      nexButton: function() {
        if (this.json.title != "") this.next = true;
        else alert("Please, write a title!");
      },
      // Return to the main
      backHome: function() {
        this.resetDate();
        this.$emit('back-home');
      },
      // Add new question to the JSON object
      processAddQuestion: function(type) {
        if (this.question != null && this.inputAnswers != null) {
          var question = {};
          var answers = this.inputAnswers.split(',');
          question.text = this.question;
          question.type = type;
          if(type==="radio") question.propal = this.inputProposals.split(',');
          question.answers = answers;
          this.part.questions.push(question);
          this.question = null;
          this.inputAnswers = null;
          this.inputProposals = null;
          return true;
        } else {
          alert("Enter a question/answer");
          return false;
        }
      },
      // Verify the input before adding question
      addQuestion: function() {
        var questionAdd = false;
        if (this.inputTitle != "") {
          this.json.title = this.inputTitle;
          this.jsonPreview = true;
        }
        if (this.typeQuestion != null) {
          if (this.typeQuestion === "input") questionAdd = this.processAddQuestion("input");
          if (this.typeQuestion === "radio") questionAdd = this.processAddQuestion("radio");
          if (questionAdd) {
            this.$refs.inputPart.disabled = true;
            this.nbQuestions++;
          }
        } else {
          alert("Please fill the form before adding!");
        }
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
  .btn-finish {
    position: absolute;
    right: 0;
  }
  .title-input {
    width: 50%;
    margin: auto;
    text-align: center;
    background-color: transparent;
    border-width: 0;
    border-bottom: 0.5px solid #33333359;
  }
  .not-center {
    text-align: initial !important;
  }
  
</style>
