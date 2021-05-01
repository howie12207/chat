<template>
  <div :style="style" class="switch_section">
    <label v-if="leftLabel" class="switch_content">{{ leftLabel }}</label>
    <label class="switch_btn">
      <input class="checkbox" v-model="syncValue" type="checkbox" />
      <span class="slider round"></span>
    </label>
    <label v-if="rightLabel" class="switch_content">{{ rightLabel }}</label>
  </div>
</template>
<script>
import Vue from "vue";
export default Vue.extend({
  name: "SwitchBtn",
  props: {
    leftLabel: {
      type: String,
      default: "",
    },
    rightLabel: {
      type: String,
      default: "",
    },
    labelColor: {
      type: String,
      default: "currentColor",
    },
    activeColor: {
      type: String,
      default: "#34D399",
    },
    inactiveColor: {
      type: String,
      default: "#E5E7EB",
    },
    width: {
      type: Number,
      default: 72,
    },
    height: {
      type: Number,
      default: 32,
    },
    value: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    syncValue: {
      get() {
        return this.value;
      },
      set(value) {
        this.$emit("input", value);
      },
    },
    style() {
      return `
                --labelColor: ${this.labelColor};
                --activeColor: ${this.activeColor};
                --inactiveColor: ${this.inactiveColor};
                --switchWidth: ${this.width}px;
                --switchHeight: ${this.height}px;
            `;
    },
  },
});
</script>

<style scoped>
* {
  box-sizing: border-box;
}
.switch_section {
  display: flex;
  align-items: center;
}
.switch_section .switch_content {
  color: var(--labelColor);
  margin: 0 8px;
}
.switch_section .switch_btn {
  position: relative;
  display: inline-block;
  width: var(--switchWidth);
  height: var(--switchHeight);
}
.switch_section .switch_btn .checkbox {
  opacity: 0;
  width: 0;
  height: 0;
}
.switch_section .switch_btn .checkbox:checked + .slider {
  background-color: var(--activeColor);
}
.switch_section .switch_btn .checkbox:focus + .slider {
  box-shadow: 0 0 1px var(--activeColor);
}
.switch_section .switch_btn .checkbox:checked + .slider:before {
  transform: translateX(calc(var(--switchWidth) - var(--switchHeight)));
}
.switch_section .switch_btn .slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: var(--inactiveColor);
  transition: 0.4s;
}
.switch_section .switch_btn .slider::before {
  position: absolute;
  content: "";
  width: calc(var(--switchHeight) - 8px);
  height: calc(var(--switchHeight) - 8px);
  left: 4px;
  bottom: 4px;
  background-color: #fff;
  transition: 0.4s;
}
.switch_section .switch_btn .slider.round {
  border-radius: var(--switchHeight);
}
.switch_section .switch_btn .slider.round:before {
  border-radius: 50%;
}
</style>
