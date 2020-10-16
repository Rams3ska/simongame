<template>
  <div class="container">
    <GameButton
      v-for="(button, index) in buttonsState"
      :key="index"
      :style="styles[index]"
      :audio="audio[index]"
      :isActive="buttonsState[index]"
      :isStarted="isStarted"
      :buttonId="index"
      :isSequencePlaying="isSequencePlaying"
      @onTurnedButton="onTurnedButton"
    />
    <div
      class="sphere"
      v-if="isLose.missClick"
      :class="{ error: isLose.missClick }"
    >
      Lose!
    </div>
    <div
      class="sphere"
      v-else-if="isLose.slowed"
      :class="{ error: isLose.slowed }"
    >
      Slowed!
    </div>
    <div class="sphere" v-else-if="isApprove" :class="{ correct: isApprove }">
      Correct!
    </div>
    <div class="sphere" v-else></div>
  </div>
</template>

<script>
import GameButton from "./GameButton";

export default {
  props: {
    isStarted: Boolean,
    sequence: Array,
    missClick: Boolean,
    isLose: {
      missClick: Boolean,
      slowed: Boolean,
    },
    isApprove: Boolean,
  },
  data: () => ({
    audio: [],
    isSequencePlaying: false,
    buttonsState: [false, false, false, false],
    styles: [
      {
        backgroundColor: "rgb(84, 202, 84)",
        borderRadius: "100% 0% 0% 0%",
      },
      {
        backgroundColor: "rgb(252, 102, 102)",
        borderRadius: "0% 100% 0% 0%",
      },
      {
        backgroundColor: "royalblue",
        borderRadius: "0% 0% 0% 100%",
      },
      {
        backgroundColor: "yellow",
        borderRadius: "0% 0% 100% 0%",
      },
    ],
  }),
  methods: {
    onTurnedButton(id) {
      if (!this.isStarted) return;
      this.$set(this.buttonsState, id, true);
      setTimeout(() => this.$set(this.buttonsState, id, false), 300);
      this.audio[id].play();
    },

    playSequence() {
      this.isSequencePlaying = true;

      let cc = 0;
      let interval = setInterval(() => {
        if (!this.isSequencePlaying || !this.isStarted) {
          clearInterval(interval);
          return;
        }
        this.onTurnedButton(this.sequence[cc]);
        cc++;

        if (cc >= this.sequence.length) {
          clearInterval(interval);
          this.isSequencePlaying = false;
          setTimeout(() => {
            this.$root.$emit("onStartModeTimeOut");
          }, 600);
        }
      }, 600);
    },

    waitAnswer() {},
  },
  components: {
    GameButton,
  },
  mounted() {
    this.audio = [
      new Audio(require("../../assets/audio/1.ogg")),
      new Audio(require("../../assets/audio/2.ogg")),
      new Audio(require("../../assets/audio/4.mp3")),
      new Audio(require("../../assets/audio/3.ogg")),
    ];
  },
};
</script>

<style lang="scss">
.error {
  background-color: rgb(245, 117, 117) !important;
}
.correct {
  background-color: rgb(117, 245, 124) !important;
  color: black;
}

.container {
  position: relative;

  width: 700px;
  height: 500px;

  background-color: #f9f9f9;

  display: grid;
  grid-template-rows: 1fr 1fr;
  grid-template-columns: 1fr 1fr;
  grid-gap: 2vw;

  padding: 20px;
  box-sizing: border-box;

  border-radius: 5px 0px 0px 5px;
}

.sphere {
  position: fixed;
  width: 100px;
  height: 100px;
  background-color: #f9f9f9;
  border-radius: 100%;
  margin-left: 207px;
  margin-top: 175px;
  border: #757b7b 5px solid;

  color: white;

  display: flex;
  align-content: center;
  justify-content: center;
  align-items: center;

  font-size: 24px;
  font-weight: bold;

  transition: background-color 0.35s;
}
</style>