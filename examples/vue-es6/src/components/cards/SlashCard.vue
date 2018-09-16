<template>
  <div :class="{active: active}">
    <span>past every </span>
    <input type="text" v-model="step" v-on="listeners">
    <span> {{fieldName}} from </span>
    <input type="text" v-model="start" v-on="listeners">
    <span> through {{range[1]}} </span>
  </div>
</template>

<script>
const cardName = "SLASH";
const SPECIFIC_CHAR = "/";

export default {
  name: "SlashCard",
  props: {
    fieldName: {
      type: String,
      default: ""
    },
    value: {
      type: String,
      default: ""
    },
    range: {
      type: Array,
      return: [0, 0]
    }
  },
  data() {
    return {
      errorMsg: "",
      active: false,
      start: null,
      step: null
    };
  },
  watch: {
    value() {
      if (this.mayResolve()) {
        this.errorMsg = "";
        this.resolve();
        if (!this.hasErrorMsg()) {
          this.$emit("resolved", this.getCronText());
        }
      } else {
        this.reset();
      }
      this.$emit("mistakes", this.errorMsg);
    }
  },
  computed: {
    listeners() {
      return {
        ...this.$listeners,
        input: () => this.$emit("update:card", this.getCronText())
      };
    }
  },
  methods: {
    reset() {
      this.active = false;
      this.errorMsg = "";
      this.start = null;
      this.step = null;
    },
    hasErrorMsg() {
      return !!("string" === typeof this.errorMsg
        ? this.errorMsg.length
        : false);
    },
    mayResolve() {
      const result = this.hasSpecificChar(SPECIFIC_CHAR);
      if (result) {
        this.active = true;
      }
      return result;
    },
    validate() {
      if (this.start < this.range[0] || this.start > this.range[1]) {
        this.errorMsg = `The start value have to be in [${this.range[0]},${
          this.range[1]
        }]`;
        return;
      }
      if (this.step < this.range[0] + 1 || this.step > this.range[1] + 1) {
        this.errorMsg = `The step value have to be in [${this.range[0] +
          1},${this.range[1] + 1}]`;
      }
    },
    hasSpecificChar(c) {
      return "string" === typeof this.value
        ? this.value.indexOf(c) >= 0
        : false;
    },
    resolve() {
      if (!this.mayResolve()) {
        return;
      }
      const stepValues = this.value.split(SPECIFIC_CHAR);
      if (
        2 !== stepValues.length ||
        "" === stepValues[0].trim() ||
        "" === stepValues[1].trim()
      ) {
        this.errorMsg =
          'This field has the character "/" while can not match "A/B" form.';
        return;
      }
      this.start = parseInt(stepValues[0]);
      this.step = parseInt(stepValues[1]);
      this.validate();
    },
    getCronText() {
      const start = this.start ? this.start : "";
      const step = this.step ? this.step : "";
      return start + SPECIFIC_CHAR + step;
    },
    getResolvedMeaning() {}
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../../public/variables.scss";

.active {
  color: $warn;
}
</style>
