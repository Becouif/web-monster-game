<template>
  <div id="game">
    <section id="player">
      <h2>Player's Health</h2>
      <div class="healthbar">
        <svg
          @change="animateTrans"
          class="healthbar-value"
          :style="playerBarStyles"
        ></svg>
      </div>
    </section>
    <section id="monster">
      <h2>Monster's Health</h2>
      <div class="healthbar">
        <svg class="healthbar-value" :style="monsterBarStyles"></svg>
      </div>
    </section>
    <!-- section for log  -->
    <section>
      <ul>
        <li>{{ logMessages.actionBy }}</li>
        <li>{{ logMessages.actionType }}</li>
        <li>{{ logMessages.actionValue }}</li>
      </ul>
    </section>
    <!-- start of button to control the game  -->
    <section>
      <button @click="attackMonster">ATTACK</button>
      <button @click="specialAttack">SPECIAL ATTACK</button>
      <button @click="healPlayer">HEAL</button>
      <button @click="resetGame">SURRENDER</button>
    </section>
  </div>
</template>

<style scoped>
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
  margin: 0.25rem;
  color: #fde5e5;
}
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>

<script setup>
import { ref, computed } from 'vue';

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
  const attackValue = getRandomValue(5, 15);
  playerHealth.value -= attackValue;
  addLogMessage('monster', 'attack', attackValue);
}
function attackMonster() {
  currentRound.value++;
  const attackValue = getRandomValue(5, 12);
  monsterHealth.value -= attackValue;

  addLogMessage('player', 'attack', attackValue);
  setTimeout(() => {
    attackPlayer();
  }, 2000);
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
  }, 2000);
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
