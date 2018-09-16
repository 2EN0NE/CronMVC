<template>
  <div :class="{active: active}">
    <span v-for="(item, index) in cRange" :key="index">
      <input type="checkbox" v-model="cardInputData[index]" v-on="listeners">
      <label> {{cRange[index]}} </label>
    </span>
  </div>
</template>

<script>
const SPECIFIC_CHAR = ",";

export default {
  name: "CommaCard",
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
      cardInputData: Array(this.range[1] - this.range[0] + 1).fill(false)
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
    },
  },
  computed: {
    cRange() {
      return Array(this.range[1] - this.range[0] + 1)
        .fill(0)
        .map((v, i) => i + this.range[0]);
    },
    listeners () {
      return {
        ...this.$listeners,
        input: event => this.$emit("update:card", this.getCronText())
      }
    }
  },
  methods: {
    reset() {
      this.active = false;
      this.errorMsg = "";
      const cardInputHasValue = this.cardInputData.filter(item => {
        return item;
      }).length;
      if (cardInputHasValue) {
        this.cardInputData = Array(this.range[1] - this.range[0] + 1).fill(
          false
        );
      }
    },
    hasErrorMsg() {
      return !!("string" === typeof this.errorMsg
        ? this.errorMsg.length
        : false);
    },
    mayResolve() {
      const result =
        this.hasSpecificChar(SPECIFIC_CHAR) || /^\d+$/.test(this.value);
      if (result) {
        this.active = true;
      }
      return result;
    },
    validate() {
      const chars = this.value.split(SPECIFIC_CHAR);
      let num;
      chars.forEach(ele => {
        if (!/^\d+$/.test(ele)) {
          this.errorMsg =
            'the value that seperated by the "," have to be the positive integer!';
          return;
        }
        num = parseInt(ele);
        if (num < this.range[0] || num > this.range[1]) {
          this.errorMsg = `The first value have to be in [${this.range[0]},${
            this.range[1]
          }]`;
          return;
        }
      });
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
      this.validate();
      if (this.hasErrorMsg()) {
        return;
      }
      this.cardInputData.fill(false);
      const chars = this.value.split(SPECIFIC_CHAR);
      let num;
      chars.forEach(ele => {
        num = parseInt(ele);
        if (typeof num === "number" && !isNaN(num)) {
          this.cardInputData[num] = true;
        }
      });
    },
    getCronText() {
      const foo = [];
      this.cardInputData.forEach((e, i) => {
        if (e) {
          foo.push(i);
        }
      });
      return foo.join(",");
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
