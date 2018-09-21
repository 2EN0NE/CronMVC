<template>
  <div class="card" :class="{active: active}" @click="$emit('update:card', '*')">
    <span>every {{fieldName}}</span>
  </div>
</template>

<script>
const SPECIFIC_CHAR = "*";

export default {
  name: "AsteriskCard",
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
        if (!this.hasErrorMsg()) {
          this.$emit("resolved:card", this.getCronText());
        }
      } else {
        this.reset();
      }
      this.$emit("mistakes", this.errorMsg);
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
        this.errorMsg = 'This field has more than ONE character besides "*"!';
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
    getResolvedMeaning() {}
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../../public/variables.scss";

.card {
  padding: 5px;
}
.active {
  color: $vue-green;
}
</style>
