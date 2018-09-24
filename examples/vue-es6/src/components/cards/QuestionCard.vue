<template>
  <div :class="{unactive: !active}" @click="handleInput">
    <span>(NOT SET)</span>
  </div>
</template>

<script>
const cardName = "QUESTION";
const SPECIFIC_CHAR = "?";

export default {
  name: "QuestionCard",
  props: {
    fieldName: {
      type: String,
      default: ""
    },
    value: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      errorMsg: "",
      active: false
    };
  },
  watch: {
    value() {
      if (this.mayResolve()) {
        this.errorMsg = "";
        this.resolve();
        if (this.hasErrorMsg()) {
          this.$emit("error", this.errorMsg);
        } else {
          const _this = this;
          _this.$emit("resolved", {
            cardName: cardName,
            cronText: _this.getCronText(),
            resolved: _this.getResolvedMeaning()
          });
        }
      } else {
        this.reset();
      }
    }
  },
  computed: {},
  methods: {
    reset() {
      this.active = false;
      this.errorMsg = "";
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
      if (this.mayResolve() && this.value.length > 1) {
        this.errorMsg = 'This field has more than ONE character besides "?"!';
      }
    },
    hasSpecificChar(c) {
      return "string" === typeof this.value
        ? this.value.indexOf(c) >= 0
        : false;
    },
    resolve() {
      this.validate();
    },
    getCronText() {
      return SPECIFIC_CHAR;
    },
    getResolvedMeaning() {
      return "";
    },
    handleInput() {
      this.$emit("update:card", {
        cardName: cardName,
        cronText: SPECIFIC_CHAR
      });
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../../public/variables.scss";

.unactive {
  color: $grey6;
}
</style>
