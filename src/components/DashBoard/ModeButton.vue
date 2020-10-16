<template>
  <span class="setting-param" :class="{ active: isActive }" @click="setActive">
    {{ name }}
  </span>
</template>

<script>
export default {
  props: {
    asMode: Number,
    name: String,
    isActive: Boolean,
  },
  methods: {
    setActive() {
      if (this.isActive) return;
      this.$root.$emit("changeMode", this.asMode);
    },
  },
  mounted() {},
};
</script>

<style lang='scss' scoped>
.setting-param {
  background-color: rgb(209, 209, 209);
  padding: 3px;

  transition: color 0.35s, border-color 0.35s, background-color 0.35s;

  &:hover {
    cursor: pointer;

    background-color: powderblue;
  }

  margin: 2px;
  border-radius: 3px;
}

.active {
  background-color: #5feca9;
}
</style>