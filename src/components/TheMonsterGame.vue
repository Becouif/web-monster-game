<template>
  <div id="bar">
    <div id="game-grid">
      <section id="player">
        <h2>Player's Health</h2>
        <div class="healthbar shadow-2xl">
          <svg
            @change="animateTrans"
            class="healthbar-value shadow-2xl"
            :style="playerBarStyles"
          ></svg>
        </div>

        <i class="fa-sharp fa-solid fa-alien-8bit"></i>
      </section>
      <section id="monster">
        <h2>Monster's Health</h2>
        <div class="healthbar shadow-2xl">
          <svg
            class="healthbar-value shadow-2xl"
            :style="monsterBarStyles"
          ></svg>
        </div>
      </section>
    </div>
  </div>

  <div id="game-pad">
    <!-- section for log  -->

    <!-- start of button to control the game  -->
    <section id="container">
      <div id="controls">
        <the-button-vue
          id="attack-btn"
          class="move-control"
          @click="attackMonster"
          >ATTACK</the-button-vue
        >
        <the-button-vue
          class="move-control"
          id="special-btn"
          @click="specialAttack"
          >SPECIAL ATTACK</the-button-vue
        >
        <the-button-vue class="move-control" id="heal-btn" @click="healPlayer"
          >HEAL</the-button-vue
        >
        <the-button-vue class="move-control" id="reset-btn" @click="resetGame"
          >SURRENDER</the-button-vue
        >
      </div>
    </section>
    <section id="log">
      <ul v-for="logMessage in logMessages">
        <li>{{ logMessage.actionBy }}</li>
        <li>{{ logMessage.actionType }}</li>
        <li>{{ logMessage.actionValue }}</li>
      </ul>
    </section>
  </div>
</template>

<style scoped>
#game-pad {
  text-align: center;
  margin: auto;
  /* align-items: center; */
  /* justify-content: center; */
  /* display: flex; */
}
#control {
}
.move-control {
  margin: 0.15rem;
}
.player-icon {
  color: #fde5e5;
}
#bar {
  /* padding: 0.5rem; */
  width: 100%;
  /* margin-right: 10rem; */
  /* justify-content: center; */
  /* justify-items: center; */
  text-align: center;
}
#game-grid {
  margin: 2rem;
  display: grid;
  grid-template-columns: 50% 50%;
  gap: 1rem;
}
.healthbar {
  width: 100%;
  height: 40px;
  border: 1px solid #575757;
  margin: 1rem 0;
  background-color: #fde5e5;
}
.healthbar-value {
  background-color: #00a876;
  width: 100%;
  height: 100%;
}
#monster h2,
#player h2 {
  margin-left: -0.25rem;
  color: #fde5e5;
}
#log ul {
  list-style: none;
  margin: 0;
  padding: 0;
  color: #000;
}
li {
  margin: 0.5rem 0;
  color: #000;
}
.log-player {
  color: #7700ff;
}
.log-monster {
  color: #da8d00;
}
.log-damage {
  color: red;
}
.log-heal {
  color: green;
}
</style>

<script setup>
import TheButtonVue from './TheButton.vue';
import { ref, computed } from 'vue';

components: {
  TheButtonVue;
}
const playerHealth = ref(100);
const monsterHealth = ref(100);
const currentRound = ref(0);
const winner = ref(null);
const logMessages = ref([]);

function resetGame() {
  playerHealth.value = 100;
  monsterHealth.value = 100;
  currentRound.value = 0;
  winner.value = null;
  logMessages.value = [];
}

function addLogMessage(who, what, value) {
  logMessages.value.unshift({
    actionBy: who,
    actionType: what,
    actionValue: value,
  });
}
// start of functions for the game
function getRandomValue(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}
function attackPlayer() {
  const attackValue = getRandomValue(5, 13);
  playerHealth.value -= attackValue;
  addLogMessage('monster', 'attack', attackValue);
}
function attackMonster() {
  currentRound.value++;
  const attackValue = getRandomValue(5, 15);
  monsterHealth.value -= attackValue;

  addLogMessage('player', 'attack', attackValue);
  setTimeout(() => {
    attackPlayer();
  }, 1000);
  console.log(logMessages.value);
}
function healPlayer() {
  const attackValue = getRandomValue(5, 12);
  playerHealth.value += attackValue;
  addLogMessage('player', 'heals', attackValue);
}

function specialAttack() {
  currentRound.value++;
  const attackValue = getRandomValue(10, 15);
  monsterHealth.value -= attackValue;

  addLogMessage('player', 'special-attack', attackValue);
  setTimeout(() => {
    attackPlayer();
  }, 1000);
}
// function for special attack

// start of computed
const monsterBarStyles = computed(function () {
  if (monsterHealth.value < 0) {
    return { width: '0%' };
  }
  return { width: monsterHealth.value + '%' };
});
const playerBarStyles = computed(function () {
  if (playerHealth.value < 0) {
    return { width: '0%' };
  }
  return { width: playerHealth.value + '%' };
});
</script>
