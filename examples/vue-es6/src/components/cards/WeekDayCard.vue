<template>
  <div :class="{unactive: !active}">
      <span> the nearest weekday to the <input type="text" v-model="dayOfMonth" v-on="listeners" style="width:3ch">{{xthMap}} of the month </span>
  </div>
</template>

<script>
const cardName = "WEEKDAY";
const SPECIFIC_CHAR = "W";

export default {
  name: "WeekDayCard",
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
      active: false,
      dayOfMonth: null
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
          this.$emit("resolved", {
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
    listeners() {
      const _this = this;
      return {
        ..._this.$listeners,
        input: () =>
          _this.$emit("update:card", {
            cardName: cardName,
            cronText: _this.getCronText()
          })
      };
    },
    xthMap() {
      const xth = ["", "st", "nd", "rd"][this.dayOfMonth];
      return xth ? xth : "th";
    }
  },
  methods: {
    reset() {
      this.active = false;
      this.errorMsg = "";
      this.dayOfMonth = null;
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
      if (this.fieldName === "day") {
        if (this.dayOfMonth !== parseInt(this.dayOfMonth)) {
          this.errorMsg = "The day-of-month value have to be INTEGER";
          return;
        }
        if (this.dayOfMonth < 1 || this.dayOfMonth > 31) {
          this.errorMsg = "The day-of-month value have to be in [1,31]";
          return;
        }
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
      const index = this.value.indexOf(SPECIFIC_CHAR);
      if (index > 0) {
        this.dayOfMonth = parseInt(this.value.substr(0, index));
      }
      this.validate();
    },
    getCronText() {
      return this.dayOfMonth + SPECIFIC_CHAR;
    },
    getResolvedMeaning() {
      return ` the nearest weekday to the ${this.dayOfMonth}${this.xthMap} of the month `;
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
