<template>
  <div :style="style" :class="['nav_icon', { active }]" @click="menuHandle">
    <div class="line line1"></div>
    <div class="line line2"></div>
    <div class="line line3"></div>
  </div>
</template>

<script>
import Vue from "vue";

export default Vue.extend({
  name: "IconMenu",
  props: {
    size: {
      type: Number,
      default: 32,
    },
    mainColor: {
      type: String,
      default: "currentColor",
    },
  },
  data() {
    return {
      active: false,
    };
  },
  computed: {
    style() {
      return {
        "--menuSize": `${this.size}px`,
        "--menuColor": this.mainColor,
        "--move": `${this.size / 4 + 1}px`,
        "--move2": `-${this.size / 4 + 1}px`,
      };
    },
  },
  methods: {
    menuHandle() {
      this.active = !this.active;
      this.$emit("menuHandle", this.active);
    },
  },
});
</script>

<style scoped>
* {
  box-sizing: border-box;
}
.nav_icon {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  align-items: center;
  width: var(--menuSize);
  height: var(--menuSize);
  cursor: pointer;
}
.nav_icon .line {
  width: var(--menuSize);
  border-radius: 4px;
  border: 2px solid var(--menuColor);
  background: var(--menuColor);
  transition: 0.4s;
}

.nav_icon.active .line1 {
  transform: translateY(var(--move)) rotate(45deg);
}
.nav_icon.active .line2 {
  opacity: 0;
}
.nav_icon.active .line3 {
  transform: translateY(var(--move2)) rotate(-45deg);
}
</style>
