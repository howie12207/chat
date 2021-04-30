<template>
  <div :style="style" class="icon_more"></div>
</template>

<script>
import Vue from "vue";
export default Vue.extend({
  name: "IconMore",
  props: {
    mainColor: {
      type: String,
      default: "currentColor",
    },
    size: {
      type: Number,
      default: 4,
    },
  },
  computed: {
    style() {
      return {
        "--moreColor": this.mainColor,
        "--moreSize": `${this.size}px`,
        "--moreMargin": `${this.size * 2}px`,
        "--morePosition": `-${this.size + this.size / 2}px`,
      };
    },
  },
});
</script>

<style scoped>
.icon_more {
  display: inline-block;
  position: relative;
  margin: 0 var(--moreMargin);
}
.icon_more,
.icon_more::after,
.icon_more::before {
  box-sizing: border-box;
  width: var(--moreSize);
  height: var(--moreSize);
  background: var(--moreColor);
  border-radius: 50%;
}
.icon_more::after,
.icon_more::before {
  content: "";
  position: absolute;
  top: 0;
}
.icon_more::after {
  left: var(--morePosition);
}
.icon_more::before {
  right: var(--morePosition);
}
</style>
