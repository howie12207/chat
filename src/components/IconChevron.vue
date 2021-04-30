<template>
  <div :style="style" :class="['icon_chevron', direction]"></div>
</template>

<script>
import Vue from "vue";
export default Vue.extend({
  name: "IconChevron",
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
    lineWidth: {
      type: Number,
      default: 2,
    },
  },
  computed: {
    style() {
      return {
        "--chevronColor": this.mainColor,
        "--chevronSize": `${this.size}px`,
        "--chevronLineWidth": `${this.lineWidth}px`,
      };
    },
  },
});
</script>

<style scoped>
.icon_chevron {
  display: inline-block;
  position: relative;
  width: var(--chevronSize);
  height: var(--chevronSize);
  box-sizing: border-box;
}
.icon_chevron:after {
  content: "";
  position: absolute;
  box-sizing: border-box;
  border: solid var(--chevronColor);
  border-width: 0 var(--chevronLineWidth) var(--chevronLineWidth) 0;
  border-bottom-right-radius: 2px;
  padding: calc(calc(var(--chevronSize) - var(--chevronLineWidth)) / 3);
  top: 50%;
  left: 50%;
  transform: translate(-25%, -50%) rotate(135deg);
}
.icon_chevron.top:after {
  transform: translate(-50%, -25%) rotate(-135deg);
}
.icon_chevron.right:after {
  transform: translate(-75%, -50%) rotate(-45deg);
}
.icon_chevron.bottom:after {
  transform: translate(-50%, -75%) rotate(45deg);
}
</style>
