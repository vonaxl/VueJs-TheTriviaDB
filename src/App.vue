<template>
  <div id="app">
    <Header />
    <!-- <P>What category would you like to test?</P> -->

    <div class="container">
      <div class="row justify-content-center align-items-center" style="height:80vh">
        <h3>Score! [{{score}}]</h3>
        <div class="row">
          <h2 class="col-md-12 text-center">{{ trivia.question }}</h2>
          <div v-for="choice in choices" v-bind:key="choice" class="col-md-6">
            <button @click="check(choice)" class="w-100" :disabled="clicked">{{choice}}</button>
          </div>
        </div>
        <button @click="nextTest">Next Trivia</button>
      </div>
    </div>

    <Footer />
  </div>
</template>

<script>
// import nav from "./components/Nav.vue";
import Header from "./components/layout/Header";
import Footer from "./components/layout/Footer";
// import Trivias from "./components/Trivias";
import axios from "axios";
export default {
  name: "App",
  components: {
    Header,
    Footer
    // Trivias
  },
  data() {
    return {
      trivia: [],
      choices: [],
      score: 0,
      correctAnswer: "",
      clicked: false
    };
  },
  created() {
    this.nextTest();
  },
  methods: {
    reset() {
      this.choices = [];
      this.correctAnswer = "";
      this.clicked = false;
    },
    nextTest() {
      axios
        .get("https://opentdb.com/api.php?amount=1")
        .then(res => {
          this.trivia = res.data.results[0];
          // this.trivia.replace(/&quot;/g, '"');
          this.choicesLoad(
            this.trivia["correct_answer"],
            this.trivia["incorrect_answers"]
          );
        })
        .catch(err => console.log(err));
    },
    choicesLoad(choice1, choice2) {
      this.reset();
      this.correctAnswer = choice1;
      this.choices.push(choice1);
      choice2.forEach(choice => {
        this.choices.push(choice);
      });
      this.shuffle(this.choices);
      console.log(this.choices);
    },
    shuffle(a) {
      var j, x, i;
      for (i = a.length - 1; i > 0; i--) {
        j = Math.floor(Math.random() * (i + 1));
        x = a[i];
        a[i] = a[j];
        a[j] = x;
      }
      return a;
    },
    check(answer) {
      if (answer === this.correctAnswer) {
        this.clicked = true;
        this.score++;
        alert("Goodjob!");
      } else {
        alert("WRONG!");
      }
      setTimeout(this.nextTest(), 1000);
    }
  }
};
</script>

<style>
</style>
