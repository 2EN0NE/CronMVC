<template>
  <div :class="{unactive: !active}">
    <span v-for="(item, index) in cRange" :key="index">
      <input type="checkbox" :checked="cardInputData[index]" v-on:input="handleInput[index]">
      <label> {{cRange[index]}} </label>
    </span>
  </div>
</template>

<script>
const cardName = "COMMA";
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
  computed: {
    cRange() {
      return Array(this.range[1] - this.range[0] + 1)
        .fill(0)
        .map((v, i) => i + this.range[0]);
    },
    handleInput() {
      const _this = this;
      return Array(this.range[1] - this.range[0] + 1)
        .fill(0)
        .map((v, i) => {
          return function(e) {
            _this.cardInputData[i] = e.target.checked;
            _this.$emit("update:card", {
              cardName: cardName,
              cronText: _this.getCronText()
            });
          };
        });
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
          this.cardInputData[num - this.range[0]] = true;
        }
      });
    },
    getCronText() {
      const foo = [];
      this.cardInputData.forEach((e, i) => {
        if (e) {
          foo.push(this.cRange[i]);
        }
      });
      return foo.join(",");
    },
    getResolvedMeaning() {
      const pre = this.resolvedPreText();
      const body = this.resolvedBodyText();
      return pre + body;
    },
    resolvedPreText() {
      if (this.fieldName === "year") {
        return "on ";
      } else if (this.fieldName === "week") {
        return "on ";
      } else if (this.fieldName === "month") {
        return "in ";
      } else if (this.fieldName === "day") {
        return "on day-of-month ";
      } else if (this.fieldName === "hour") {
        return "past hour ";
      } else if (this.fieldName === "minute") {
        return "at minute ";
      } else if (this.fieldName === "second") {
        return "at second ";
      }
    },
    resolvedBodyText() {
      let t = this.getCronText();
      if (this.fieldName === "week") {
        t = this.mapWeekText(t);
      } else if (this.fieldName === "month") {
        t = this.mapMonthText(t);
      }
      const lastCommaIndex = t.lastIndexOf(",");
      if (~lastCommaIndex) {
        t =
          t.substr(0, lastCommaIndex) +
          " and " +
          t.substring(lastCommaIndex + 1, t.length);
      }
      return t.replace(/,/g, ", ");
    },
    mapWeekText(t) {
      return t
        .replace(/0/g, "Sunday")
        .replace(/1/g, "Monday")
        .replace(/2/g, "Tuesday")
        .replace(/3/g, "Wednesday")
        .replace(/4/g, "Thursday")
        .replace(/5/g, "Friday")
        .replace(/6/g, "Saturday")
        .replace(/7/g, "Sunday");
    },
    mapMonthText(t) {
      return t
        .replace(/12/g, "December")
        .replace(/11/g, "November")
        .replace(/10/g, "October")
        .replace(/9/g, "September")
        .replace(/8/g, "August")
        .replace(/7/g, "July")
        .replace(/6/g, "June")
        .replace(/5/g, "May")
        .replace(/4/g, "April")
        .replace(/3/g, "March")
        .replace(/2/g, "February")
        .replace(/1/g, "January");
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
