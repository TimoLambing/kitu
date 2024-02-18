<template>
  <div class="flex justify-center items-center min-h-screen bg-gradient-to-tl from-[#753682] to-[#bf2e34] p-4">
    <div class="max-w-screen-lg mx-auto">
      <h1 class="text-6xl text-center font-bold text-white mb-8">Kitu täringu mäng</h1>
      <div v-if="!gameStarted" id="name-setting" class="m-4">
        <h2 class="text-2xl text-center text-white mb-4">Kirjuta mängijate nimed</h2>
        <div class="p-4">
        <UInput
            icon="i-heroicons-user"
            autocomplete="off"
            :ui="{ icon: { trailing: { pointer: '' } } }"
            type="text"
            size="xl"
            v-model="player1Name"
            placeholder="Esimene mängija"
            class="mb-4 text-xl"
          ></UInput>
          <UInput
            icon="i-heroicons-user"
            autocomplete="off"
            :ui="{ icon: { trailing: { pointer: '' } } }"
            type="text"
            size="xl"
            v-model="player2Name"
            placeholder="Teine mängija"
            class="mb-4 text-white"
            theme="white"
          ></UInput>
          <UButton label="ALUSTA MÄNGU" color="white" @click="startGame" size="xl">
            <template #trailing>
              <UIcon name="i-heroicons-arrow-right-20-solid" />
            </template>
          </UButton>
        </div>
      </div>
      <main v-else class="bg-white/35 backdrop-blur-[200px] shadow-xl rounded-lg overflow-hidden m-4 flex flex-col md:flex-row">
        <section :class="['player', 'player--0', {'player--active': activePlayer === 0, 'player--winner': winner === 0}]">
        <h2 class="name">{{ playerNames[0] }}</h2>
        <p class="score">{{ scores[0] }}</p>
        <p class="winner-message" v-if="winner === 0">Võitja!</p>
        <div class="current">
          <p class="current-label">Kogutud</p>
          <p class="current-score">{{ activePlayer === 0 ? currentScore : 0 }}</p>
        </div>
      </section>
        <section :class="['player', 'player--1', {'player--active': activePlayer === 1, 'player--winner': winner === 1}]">
        <h2 class="name">{{ playerNames[1] }}</h2>
        <p class="score">{{ scores[1] }}</p>
        <p class="winner-message" v-if="winner === 1">Võitja!</p>
        <div class="current">
          <p class="current-label">Kogutud</p>
          <p class="current-score">{{ activePlayer === 1 ? currentScore : 0 }}</p>
        </div>
      </section>

      <img v-if="dice !== 0" :src="`/dice-${dice}.png`" alt="Playing dice" class="dice w-auto max-w-xs mx-auto my-4 md:my-0 md:max-w-sm" />
        <div class="flex flex-col items-center space-y-4 p-4">
          <button class="btn btn--new" @click="resetGame">🔄 Mängi uuesti</button>
          <button class="btn btn--roll" :disabled="winner !== null" @click="rollDice">🎲 Veereta täringuid</button>
          <button class="btn btn--hold" :disabled="winner !== null" @click="holdScore">📥 Pane punktid hoiule</button>
        </div>
      </main>
    </div>
  </div>
</template>


<script setup>
import { ref } from 'vue'

const player1Name = ref('')
const player2Name = ref('')
const gameStarted = ref(false)
const scores = ref([0, 0])
const currentScore = ref(0)
const activePlayer = ref(0)
const dice = ref(0)
const winner = ref(null)
const playerNames = ref(['Player 1', 'Player 2'])

function startGame() {
  gameStarted.value = true
  playerNames.value = [player1Name.value || 'Player 1', player2Name.value || 'Player 2']
  resetGame()
}

function resetGame() {
  scores.value = [0, 0]
  currentScore.value = 0
  activePlayer.value = 0
  winner.value = null
  dice.value = 0
}

function rollDice() {
  dice.value = Math.floor(Math.random() * 6) + 1
  if (dice.value !== 1) {
    currentScore.value += dice.value
  } else {
    switchPlayer()
  }
}

function holdScore() {
  scores.value[activePlayer.value] += currentScore.value
  if (scores.value[activePlayer.value] >= 20) { // Winning score
    winner.value = activePlayer.value
  } else {
    switchPlayer()
  }
}

function switchPlayer() {
  currentScore.value = 0
  activePlayer.value = activePlayer.value === 0 ? 1 : 0
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Nunito&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
}

html {
  font-size: 62.5%;
  box-sizing: border-box;
}

body {
  font-family: 'Nunito', sans-serif;
  font-weight: 400;
  height: 100vh;
  color: #333;
  background-image: linear-gradient(to top left, #753682 0%, #bf2e34 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}

/* LAYOUT */
main {
  position: relative;
  width: 100rem;
  height: 60rem;
  background-color: rgba(255, 255, 255, 0.35);
  backdrop-filter: blur(200px);
  filter: blur();
  box-shadow: 0 3rem 5rem rgba(0, 0, 0, 0.25);
  border-radius: 9px;
  overflow: hidden;
  display: flex;
}

.player {
  flex: 1;
  flex: 50%;
  padding: 9rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: all 0.75s;
  box-sizing: border-box;
}

/* ELEMENTS */
.name {
  position: relative;
  font-size: 4rem;
  text-transform: uppercase;
  letter-spacing: 1px;
  word-spacing: 2px;
  font-weight: 300;
  margin-bottom: 1rem;
}

.score {
  font-size: 8rem;
  font-weight: 300;
  color: #c7365f;
  margin-bottom: auto;
}

.player--active {
  background-color: rgba(255, 255, 255, 0.4);
  width: 100%;
}
.player--active .name {
  font-weight: 700;
}
.player--active .score {
  font-weight: 400;
}

.player--active .current {
  opacity: 1;
}

.current {
  background-color: #c7365f;
  opacity: 0.8;
  border-radius: 9px;
  color: #fff;
  width: 65%;
  padding: 2rem;
  text-align: center;
  transition: all 0.75s;
}

.current-label {
  text-transform: uppercase;
  margin-bottom: 1rem;
  font-size: 1.7rem;
  color: #ddd;
}

.current-score {
  font-size: 3.5rem;
}

/* ABSOLUTE POSITIONED ELEMENTS */
.btn {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  color: #444;
  background: none;
  border: none;
  font-family: inherit;
  font-size: 1.8rem;
  text-transform: uppercase;
  cursor: pointer;
  font-weight: 400;
  transition: all 0.2s;

  background-color: white;
  background-color: rgba(255, 255, 255, 0.6);
  backdrop-filter: blur(10px);

  padding: 0.7rem 2.5rem;
  border-radius: 50rem;
  box-shadow: 0 1.75rem 3.5rem rgba(0, 0, 0, 0.1);
}

.btn::first-letter {
  font-size: 2.4rem;
  display: inline-block;
  margin-right: 0.7rem;
}

.btn--new {
  top: 4rem;
}
.btn--roll {
  top: 39.3rem;
}
.btn--hold {
  top: 46.1rem;
}

.btn:active {
  transform: translate(-50%, 3px);
  box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.15);
}

.btn:focus {
  outline: none;
}

.dice {
  position: absolute;
  left: 50%;
  top: 16.5rem; /* Adjust if necessary to fit your design */
  transform: translateX(-50%);
  max-width: 100px; /* Example max-width, adjust as necessary */
  width: 30%; /* Responsive width */
  max-height: auto; /* Maintain aspect ratio */
  box-shadow: 0 2rem 5rem rgba(0, 0, 0, 0.2);
}

.player--winner {
  background-color: #2ff05f;
}

.player--winner .name {
  font-weight: 700;
  color: #c7365f;
}

.hidden {
  display: none;
}

.winner-message {
  font-size: 5rem; /* Adjust the size as needed */
  color: white;
  font-weight: bold;
  text-align: center;
  margin-top: 10px; /* Adds some space above the winner message */
}

/* Adjustments for Responsiveness */
@media (max-width: 768px) {
  main {
    width: auto; /* Adjusts width to be auto to fit smaller screens */
    height: auto; /* Allows height to adjust based on content */
    flex-direction: column; /* Stacks children vertically */
    padding: 2rem; /* Reduces padding */
  }

  .player {
    padding: 3rem 1rem; /* Reduces padding */
    flex: none; /* Removes flex sizing */
    width: 100%; /* Ensures full width */
    align-items: center; /* Center alignment for smaller screens */
    margin-bottom: 2rem; /* Adds space between players */
  }

  .name, .score, .current-score, .current-label {
    font-size: 2rem; /* Adjusts font size for readability */
  }

  .dice {
  /* Existing styles */
  position: relative; /* or 'absolute' depending on your layout needs */
  left: auto;
  top: auto;
  transform: none;
  margin: 20px auto;
  width: 50%; /* Adjust as necessary */
  max-width: 80px; /* Prevent the image from being too large on small screens */
  z-index: 10; /* Ensure the dice is above the buttons */
}

  .btn {
    position: relative; /* Overrides absolute positioning */
    left: auto; /* Resets left positioning */
    transform: none; /* Removes transform */
    margin: 10px auto; /* Centers the button and adds margin for spacing */
    padding: 0.5rem 1.5rem; /* Adjusts padding */
    font-size: 1.5rem; /* Adjusts font size for smaller screens */
  }

  .btn--new, .btn--roll, .btn--hold {
    top: auto; /* Ensures buttons are positioned in flow */
  }
}

</style>