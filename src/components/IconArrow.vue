<template>
  <div :style="style" :class="['icon_arrow', direction]"></div>
</template>

<script>
import Vue from "vue";
export default Vue.extend({
  name: "IconArrow",
  props: {
    mainColor: {
      type: String,
      default: "currentColor",
    },
    size: {
      type: Number,
      default: 24,
    },
    direction: {
      type: String,
      default: "left",
      validator: (value) => {
        return ["left", "top", "right", "bottom"].includes(value);
      },
    },
  },
  computed: {
    style() {
      return {
        "--arrowColor": this.mainColor,
        "--arrowSize": `${this.size}px`,
        "--arrowLineWidth": this.lineWidth,
      };
    },
  },
});
</script>

<style scoped>
.icon_arrow {
  display: inline-block;
  position: relative;
  box-sizing: border-box;
  width: var(--arrowSize);
  height: var(--arrowSize);
}
.icon_arrow:after,
.icon_arrow:before {
  content: "";
  position: absolute;
  box-sizing: border-box;
  left: 3px;
}
.icon_arrow:after {
  width: calc(var(--arrowSize) / 2);
  height: calc(var(--arrowSize) / 2);
  border-bottom: 2px solid var(--arrowColor);
  border-left: 2px solid var(--arrowColor);
  transform-origin: bottom left;
  transform: rotate(45deg);
  left: 2px;
  bottom: 50%;
}
.icon_arrow:before {
  width: calc(var(--arrowSize) - 3px);
  height: 2px;
  top: 50%;
  transform: translateY(-50%);
  background-color: var(--arrowColor);
}
.icon_arrow.top {
  transform: rotate(90deg);
}
.icon_arrow.right {
  transform: rotate(180deg);
}
.icon_arrow.bottom {
  transform: rotate(270deg);
}
</style>
