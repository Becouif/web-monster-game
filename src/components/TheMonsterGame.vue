<template>
  <a class="font-bold text-center" href="/"
    ><h1 class="heading">Monster Game</h1></a
  >

  <div id="bar">
    <div id="game-grid">
      <section id="player">
        <h2 class="h2 font-bold">Player's Health</h2>
        <div class="healthbar shadow-2xl">
          <div
            @change="animateTrans"
            class="healthbar-value shadow-2xl"
            :style="playerBarStyles"
          >
            <span>{{ playerHealth.value }}</span>
          </div>
        </div>

        <i class="fa-sharp fa-solid fa-alien-8bit"></i>
      </section>
      <section id="monster">
        <h2 class="h2 font-bold">Monster's Health</h2>
        <div class="healthbar shadow-2xl">
          <svg class="healthbar-value shadow-2xl" :style="monsterBarStyles">
            {{ monsterHealth.valueOf }}
          </svg>
        </div>
      </section>
    </div>
  </div>
  <!-- section for winner and loser of the game  -->
  <section v-if="winner">
    <div class="text-center">
      <h2 class="winner">Game Over</h2>
      <p class="font-bold winner" v-if="winner === 'player'">You win!</p>
      <p class="font-bold winner" v-else-if="winner === 'monster'">
        Monster Wins
      </p>
      <p class="font-bold winner" v-else>draw</p>
      <the-button-vue class="move-control winner" @click="resetGame"
        >Restart Game</the-button-vue
      >
      <the-digital-clock-vue class="clock"></the-digital-clock-vue>
    </div>
  </section>
  <!-- start of button to control the game  -->
  <div v-else id="game-pad">
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
        <the-button-vue class="move-control" id="reset-btn" @click="surrender"
          >SURRENDER</the-button-vue
        >
      </div>
    </section>
  </div>
  <!-- start of log section  -->
  <section id="log">
    <ul v-for="logMessage in logMessages">
      <li
        :class="{
          'log-player': logMessage.actionBy === 'player',
          'log-monster': logMessage.actionBy === 'monster',
        }"
      >
        {{ logMessage.actionBy }}
      </li>
      <li>
        <span class="log-heal">{{ logMessage.actionType }}</span>
      </li>
      <li>{{ logMessage.actionValue }}</li>
    </ul>
  </section>
</template>

<script setup>
import TheButtonVue from './TheButton.vue';
import TheDigitalClockVue from './TheDigitalClock.vue';
import { ref, computed, watch } from 'vue';

components: {
  TheButtonVue;
  TheDigitalClockVue;
}
const playerHealth = ref(100);
const monsterHealth = ref(100);
const currentRound = ref(0);
const winner = ref(null);
const logMessages = ref([]);

function surrender() {
  winner.value = 'monster';
}
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
// start of functions/methods for the game
function getRandomValue(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
}
function attackPlayer() {
  currentRound.value++;
  const attackValue = getRandomValue(5, 15);
  if (playerHealth.value <= 0 || monsterHealth.value <= 0) {
    return;
  } else {
    playerHealth.value -= attackValue;
    addLogMessage('monster', 'attack', attackValue);
  }
}
function attackMonster() {
  const attackValue = getRandomValue(3, 13);
  if (monsterHealth.value <= 0 || playerHealth.value <= 0) {
    return;
  } else {
    monsterHealth.value -= attackValue;

    addLogMessage('player', 'attack', attackValue);
    setTimeout(() => {
      attackPlayer();
    }, 1000);
  }

  // console.log(logMessages.value);
}
function healPlayer() {
  currentRound.value++;
  const healValue = getRandomValue(8, 15);

  if (playerHealth.value + healValue > 100) {
    playerHealth.value = 100;
  } else {
    addLogMessage('player', 'heals', healValue);
    playerHealth.value += healValue;
  }

  setTimeout(() => {
    attackPlayer();
  }, 1000);
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
  } else if (playerHealth.value === 100) {
    return { width: '100%' };
  }
  return { width: playerHealth.value + '%' };
});
const useSpecialAttack = computed(function () {
  return currentRound.value % 2 != 0;
});
// end of computed propertics
watch(playerHealth, function () {
  if (playerHealth.value <= 0 && monsterHealth.value <= 0) {
    winner.value = 'draw';
  } else if (playerHealth.value <= 0) {
    winner.value = 'monster';
  }
});
watch(monsterHealth, function () {
  if (monsterHealth.value <= 0 && playerHealth.value <= 0) {
    winner.value = 'draw';
  } else if (monsterHealth.value <= 0) {
    winner.value = 'player';
  }
});
</script>
<style scoped>
.winner {
  font-family: 'Press Start 2P', cursive;
}
.clock {
  display: flex;
  justify-content: center;
  justify-items: center;
  font-weight: none;
}
.heading {
  padding: 0.5rem;
  font-size: 220%;
  font-family: 'Press Start 2P', cursive;
}
h2.h2 {
  color: #000;
}
#game-pad {
  font-family: 'Press Start 2P', cursive;
  text-align: center;
  margin: auto;
  /* align-items: center; */
  /* justify-content: center; */
  /* display: flex; */
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
  width: fixed;
  height: 100%;
}
#monster h2,
#player h2 {
  margin-left: -0.25rem;
  width: fit-content;
  color: #000;
  font-family: 'Press Start 2P', cursive;
}
#log {
  font-family: 'Press Start 2P', cursive;
  font-size: 0.5rem;
  /* display: block; */
  justify-items: center;
  /* justify-content: center; */
  justify-self: center;
  margin-left: 50%;
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
