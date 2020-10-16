<template>
  <div class="tile" :class="{ active: isActive }" @click="turnButton"></div>
</template>

<script>
export default {
  props: {
    buttonId: Number,
    isActive: Boolean,
    isPlaying: Boolean,
    isStarted: Boolean,
    audio: HTMLAudioElement,
  },
  data: () => ({}),
  methods: {
    turnButton() {
      if (!this.isStarted) return;
      this.onTurned();
      this.$root.$emit("onGetAnswer", this.buttonId);
    },
    onTurned() {
      this.isPlaying || this.$emit("onTurnedButton", this.buttonId);
    },
  },
  mounted() {},
};
</script>

<style lang="scss" scoped>
.tile {
  opacity: 0.6;
  transition: opacity 250ms ease, border 150ms ease;

  border: #232323 5px solid;

  &:hover {
    border: #be07b5 5px solid;
  }
}
.active {
  opacity: 1;
}
</style>