<template>
  <div class="container">
    <h1>Codebreaker</h1>
    <h4>Enter a code length between 3 and 10</h4>
    <div class="codeWrap">
      <input type="number" v-model="codeLength" placeholder="length (3-10)" @change="generateCode" />
      <div class="codeWrap" v-if="code">
        <button class="reveal" @click="userGuess = code">{{ showCode ? 'Hide Code' : 'Show Code' }}</button>
        <p v-if="showCode" class="win">{{ code }}</p>
      </div>
    </div>
    <div v-if="code" class="guessWrap">
      <div>
        <h4>Enter your {{ codeLength }} digit guess</h4>
        <div class="codeWrap">
          <input type="text" v-model="userGuess" placeholder="Enter guess" @keyup.enter="checkGuess" />
          <button class="guess" @click="checkGuess">Guess</button>
        </div>
      </div>
      <div class="guessContainer" v-if="guesses.length > 0">
        <table>
          <tr>
            <th>Guess</th>
            <th>Correct # Count</th>
          </tr>
          <tr v-for="(guess, index) in guesses" :key="index">
            <td>
              <span v-for="(digit, dIndex) in guess.text.split('')" :key="dIndex" class="digit-group">
                <span :class="['digit', dIndex > 3 ? 'mbDigit' : '']" :style="{ backgroundColor: guess.colors[dIndex] }" @click="cycleColor(index, dIndex)">
                  {{ digit }}
                </span>
                <span v-if="dIndex < guess.text.length - 1" class="separator">-</span>
              </span>
            </td>
            <td :class="guess.correctCount > 0 ? 'correct' : ''">{{ guess.correctCount }}</td>
          </tr>
        </table>
      </div>
      <div v-if="guessedCorrectly">
       <p class="win">Congratulations! You got it in {{ guesses.length }} tries.</p>
      </div>
    </div>
    <button v-if="code" @click="regenerateCode" class="regen">New Game</button>
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
  mounted() {
    const gameState = JSON.parse(localStorage.getItem('gameState'));
    console.log(gameState);
    if (gameState) {
      this.codeLength = gameState.codeLength;
      this.code = gameState.code;
      this.guesses = gameState.guesses;
    }
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
      this.guesses.push({
        text: this.userGuess,
        correctCount: correctCount,
        colors: Array(this.userGuess.length).fill('none') // Initialize colors as 'none'  
      });
      this.userGuess = ''; // Reset guess input
      const gameState = {
        codeLength: this.codeLength,
        code: this.code,
        guesses: this.guesses
      };
      localStorage.setItem('gameState', JSON.stringify(gameState));

      // Check if all digits were guessed correctly
      if (correctCount === this.code.length) {
        this.guessedCorrectly = true;
      }
    },
    regenerateCode() {
      localStorage.removeItem('gameState'); // Clear saved game state
      this.showCode = false;
      this.generateCode();
      this.guesses = [];
      this.guessedCorrectly = false;
    },
    cycleColor(guessIndex, digitIndex) {
      const colors = ['#FF6A60', '#F7D038', '#429128', 'transparent'];
      let currentColorIndex = colors.indexOf(this.guesses[guessIndex].colors[digitIndex]);
      let nextColorIndex = (currentColorIndex + 1) % colors.length;
      this.guesses[guessIndex].colors[digitIndex] = colors[nextColorIndex];
      const gameState = {
        codeLength: this.codeLength,
        code: this.code,
        guesses: this.guesses
      };
      localStorage.setItem('gameState', JSON.stringify(gameState));
    }
  }
}
</script>

<style>
body{
  margin: 0px;
  padding: 0px;
  overflow-x: hidden;
  background-color: #E0F7FA; /* Baby blue background */
  font-family: 'Arial', sans-serif;
}
html{
  overflow-x: hidden;
  background-color: #E0F7FA; /* Baby blue background */
}
h1{
  animation: rainbowAnimation 20s infinite;
  
}
@keyframes rainbowAnimation {
    0% { color: rgb(248, 119, 119); }
    17% { color: rgb(255, 188, 62); }
    34% { color: rgb(110, 110, 20); }
    51% { color: rgb(57, 217, 57); }
    68% { color: rgb(91, 172, 188); }
    85% { color: rgb(99, 37, 144); }
    100% { color: violet; }
}
h4{
  margin-bottom: 10px;
  margin-top: 10px;
}
/* Existing styles */
input, button {
  margin: 10px;
  padding: 8px;
  font-size: 16px;
  width: 100%;
}
input{
  width: 100%;
  margin: 0px;
  border-radius: 8px;
  border-color: #FF8882; /* Light pink coral for border */
}
.guessWrap{
  width: 100%;
}
.guessContainer{
  width: 100%;
}
.codeWrap {
  display: flex;
  flex-direction: row;
  align-items: center;
  width: 100%;
}

/* New styles for centering and background */
.container {
  background-color: #E0F7FA; /* Baby blue background */
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  height: 100vh;
  overflow-x: hidden;
  padding: 18px;
  max-width: 500px;
  margin: auto;
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
  width: fit-content;
}
.win {
  color: #2d4824;
  text-align: center;
}
/* Styles for the table */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  background-color: #fff; /* White background for the table */
  box-shadow: 0 2px 15px rgba(0,0,0,0.1); /* Subtle shadow for depth */
}

th, td {
  padding: 12px;
  border-bottom: 1px solid #ccc; /* Light gray border for separation */
  text-align: left;
  line-height: normal; /* Adjust line height for better visual spacing */
}

th {
  background-color: #FF8882; /* Light pink coral for headers */
  color: white;
}

tr:last-child td {
  border-bottom: none; /* Remove bottom border from the last row */
}
.digit {
  display: inline-block;
  padding: 6px;
  margin-right: 5px; /* Space between digits and separators */
  border: 1px solid #ccc; /* Optional: adds a border around each digit */
  border-radius: 5px;
  cursor: pointer;
}
.mbDigit{
  margin-top: 10px;
}

/* Style adjustments for separators */
span.separator {
  display: inline-block; /* Treat separators like digits for consistent spacing */
  padding-right: 5px; /* Space after the separator */
}
.correct {
  color: #429128; /* Green color for correct count */
  font-weight: bold;
}
.regen{
  margin: 0 auto;
  margin-top: 12px;
  width: fit-content;
}


</style>