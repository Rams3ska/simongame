<template>
  <div id="app">
    <div class="game-container">
      <GameBox
        v-bind="{ isStarted, sequence, isLose, isApprove }"
        ref="gamebox"
      />
      <DashBoard
        v-bind="{ rounds, modeLevels, isStarted, isLose }"
        ref="dashboard"
      />
    </div>
  </div>
</template>

<script>
import GameBox from "./components/GameBox/GameBox";
import DashBoard from "./components/DashBoard/DashBoard";

export default {
  name: "App",
  data: () => ({
    modeLevels: [
      {
        name: "Easy",
        kd: 1500,
        isActive: true,
      },
      {
        name: "Medium",
        kd: 1000,
        isActive: false,
      },
      {
        name: "Hard",
        kd: 400,
        isActive: false,
      },
    ],
    currentMode: 0,
    rounds: 0,
    isStarted: false,
    isLose: {
      missClick: false,
      slowed: false,
    },
    isApprove: false,
    sequence: [],
    currentSecuence: [],
    modeTimeOut: null,
  }),
  components: {
    DashBoard,
    GameBox,
  },
  methods: {
    changeMode(mode) {
      if (this.isStarted) return;
      this.modeLevels.forEach((x) => (x.isActive = false));
      this.modeLevels[(this.currentMode = mode)].isActive = true;
      this.sequence = this.currentSecuence = [];
      this.cleanStats();
    },

    startGame() {
      this.cleanStats();
      this.nextRound();
      this.isStarted = true;
    },

    stopGame() {
      this.clearModeTimeOut();
      this.isStarted = false;
      this.sequence = this.currentSecuence = [];
    },

    cleanStats() {
      this.rounds = 0;
      this.isLose.missClick = this.isLose.slowed = false;
    },

    checkOnLose(last) {
      this.currentSecuence.push(last);

      this.clearModeTimeOut();
      this.startModeTimeOut();

      if (this.sequence.shift() != last) {
        this.isLose.missClick = true;
        this.stopGame();
        return;
      }

      if (this.sequence.length == 0) {
        this.isApprove = true;
        this.clearModeTimeOut();

        setTimeout(() => {
          this.nextRound();
          this.isApprove = false;
        }, 1000);
      }
    },

    startModeTimeOut() {
      if (this.modeTimeOut != null) this.clearModeTimeOut();

      this.modeTimeOut = setTimeout(() => {
        this.isLoseAsSlowed();
      }, this.modeLevels[this.currentMode].kd);
    },

    clearModeTimeOut() {
      clearTimeout(this.modeTimeOut);
      this.modeTimeOut = null;
    },

    isLoseAsSlowed() {
      this.isLose.slowed = true;
      this.stopGame();
    },

    nextRound() {
      if (this.rounds != 0) this.sequence = this.currentSecuence;

      this.sequence.push(Math.floor(Math.random() * 4));
      this.$refs.gamebox.playSequence();
      this.currentSecuence = [];
      this.rounds++;
    },
  },
  mounted() {
    this.$root.$on("changeMode", this.changeMode);
    this.$root.$on("onStartGame", this.startGame);
    this.$root.$on("onStopGame", this.stopGame);
    this.$root.$on("onGetAnswer", this.checkOnLose);
    this.$root.$on("onStartModeTimeOut", this.startModeTimeOut);
  },
};
</script>

<style lang="scss">
body {
  margin: 0;
}
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0;
  height: 100vh;
  background-color: #2c3e50;
  font-family: Arial, Helvetica, sans-serif;
}
.game-container {
  position: relative;

  width: 800px;
  height: 500px;

  background-color: #6c9dce;

  display: flex;
  flex-direction: row;

  border-radius: 5px;
}
</style>
