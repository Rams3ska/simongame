<template>
  <div class="container">
    <h2>Simon The Game</h2>
    <div class="info-box">
      <span
        class="text"
        v-if="(!isLose.missClick || !isLose.slowed) & isStarted"
        >Номер раунда: {{ rounds }}</span
      >
      <span class="text" v-else-if="isLose.missClick || isLose.slowed"
        >Пройдено раундов: {{ rounds }}</span
      >
      <span
        class="text"
        v-else-if="(!isLose.missClick || !isLose.slowed) & !isStarted"
        >Нажмите START для начала игры</span
      >
    </div>
    <div class="setting-box">
      <span class="text">Уровень сложности:</span>
      <div class="settings-container">
        <ModeButton
          v-for="(x, index) in modeLevels"
          :key="index"
          :name="x.name"
          :isActive="x.isActive"
          :asMode="index"
        />
      </div>
    </div>
    <div class="box-control">
      <button @click="startGame" v-if="!isStarted">START</button>
      <button @click="stopGame" v-else-if="isStarted">STOP</button>
    </div>
  </div>
</template>

<script>
import ModeButton from "./ModeButton";
export default {
  props: {
    rounds: Number,
    modeLevels: Array,
    isStarted: Boolean,
    isLose: {
      missClick: Boolean,
      slowed: Boolean,
    },
  },
  methods: {
    startGame() {
      if (this.isStarted) return;

      this.$root.$emit("onStartGame");
    },
    stopGame() {
      if (!this.isStarted) return;

      this.$root.$emit("onStopGame");
    },
  },
  mounted() {},
  components: {
    ModeButton,
  },
};
</script>

<style lang="scss" scoped>
.settings-container {
  position: relative;

  border-radius: 10px;
  // border: 1px solid gray;

  height: 25px;

  display: flex;
  justify-content: center;
  align-items: center;

  margin-top: 10px;
}

.container {
  position: relative;

  width: 300px;
  height: 500px;

  background-color: #f9f9f9;

  border-left: gray 1px solid;

  border-radius: 0px 5px 5px 0px;

  padding: 5px;
  box-sizing: border-box;

  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-content: center;

  .text {
    font-size: 20px;
  }
}

button {
  position: relative;

  width: 80%;
  height: 40px;

  background-color: #5feca9;
  color: #ffffff;

  border-radius: 50px;

  border: none;
  outline: none;
  cursor: pointer;

  font-weight: bold;
  font-size: 20px;

  transition: color 0.35s, border-color 0.35s;
  transition: 100ms;

  &:hover {
    background-color: #fff;
    color: #5feca9;
    border: 1px #5feca9 solid;
  }
  &:active {
    transition: 100ms;
    transform: scale(1.1);
  }
}
</style>