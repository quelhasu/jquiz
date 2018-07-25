<template>
  <div class="result">
    <h1> Quiz end</h1>
    <h3> Score : {{ (score/questions)*20 }} / 20  </h3>
    <p> The quiz is finished, you can restart it</p>
  </div>
</template>

<script>
export default {
  name: "Result",
  props: {
    score: Number,
    questions: Number,
    time: null
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.result {
  text-align: center;
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
