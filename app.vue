<template>
  <div class="container">
    <h1>Codebreaker Game</h1>
    <h4>Enter a code length between 3 and 10</h4>
    <div class="codeWrap">
      <input type="number" v-model="codeLength" placeholder="length (3-10)" @change="generateCode" />
      <div class="codeWrap">
        <button class="reveal" @click="showCode = !showCode">{{ showCode ? 'Hide Code' : 'Reveal Code' }}</button>
        <p v-if="showCode">Code: {{ code }}</p>
      </div>
    </div>
    <div v-if="code">
      <div class="codeContainer">
        
        <input type="text" v-model="userGuess" placeholder="Enter guess" @keyup.enter="checkGuess" />
        <button class="guess" @click="checkGuess">Guess</button>
      </div>
      <ul v-if="guesses.length > 0">
        <li v-for="(guess, index) in guesses" :key="index">
          {{ guess.text }} - Correct Numbers: {{ guess.correctCount }}
        </li>
      </ul>
      <div v-if="guessedCorrectly">
       <p class="win">Congratulations! You've guessed the code correctly.</p>
      </div>
    </div>
    <button v-if="code" @click="regenerateCode" class="regen">Regenerate Code</button>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      codeLength: 0,
      code: '',
      userGuess: '',
      showCode: false,
      guesses: [],
      guessedCorrectly: false,
    };
  },
  methods: {
    generateCode() {
      if (this.codeLength < 3 || this.codeLength > 10) {
        alert('Please enter a valid code length between 3 and 10.');
        return;
      }
      this.code = '';
      for (let i = 0; i < this.codeLength; i++) {
        this.code += Math.floor(Math.random() * 10).toString();
      }
    },
    checkGuess() {
      if (this.userGuess.length !== this.code.length) {
        alert('Your guess must be ' + this.code.length + ' digits long.');
        return;
      }
      let correctCount = 0;
      for (let i = 0; i < this.code.length; i++) {
        if (this.code[i] === this.userGuess[i]) {
          correctCount++;
        }
      }
      this.guesses.push({ text: this.userGuess, correctCount: correctCount });
      this.userGuess = ''; // Reset guess input

      // Check if all digits were guessed correctly
      if (correctCount === this.code.length) {
        this.guessedCorrectly = true;
      }
    },
    regenerateCode() {
      this.showCode = false;
      this.generateCode();
      this.guesses = [];
      this.guessedCorrectly = false;
    }
  }
}
</script>

<style>
body{
  margin: 0px;
  padding: 0px;
}
/* Existing styles */
input, button {
  margin: 10px;
  padding: 8px;
}
.codeContainer{
  display: flex;
  align-items: center;
}
.codeWrap {
  display: flex;
  flex-direction: row;
  align-items: center;
}
ul {
  list-style-type: none;
  padding: 0;
  border: 2px solid #FF8882; /* Light pink coral border */
  border-radius: 5px;
  padding: 10px;
  margin-top: 20px;
  box-shadow: 0 0 10px 0 rgba(0, 0, 0, 0.1);
}

li {
  padding: 5px;
  border-bottom: 1px solid #ccc; /* Light gray line for each guess */
}

li:last-child {
  border-bottom: none; /* Remove bottom border from the last item */
}

/* New styles for centering and background */
.container {
  background-color: #E0F7FA; /* Baby blue background */
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

button {
  background-color: #FF8882; /* Light pink coral */
  border: none;
  color: white;
  cursor: pointer;
  border-radius: 5px;
}

button:hover {
  background-color: #FF6A60; /* Slightly darker shade for hover effect */
}
.reveal {
  background-color: #ab5d25;
}
.guess {
  background-color: #429128;
}
.win {
  color: #2d4824;
  text-align: center;
}

</style>