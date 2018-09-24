<template>
  <div :class="{unactive: !active}">
    <div v-if="fieldName==='week'">
      <span> the last {{dayOfWeekMap}} </span>&nbsp;<input type="text" v-model="dayOfWeek" v-on="listeners" style="width:3ch">
    </div>
    <div v-if="fieldName==='day'" @click="handleDayInput">
      <span> last day of the month </span>
    </div>
  </div>
</template>

<script>
const cardName = "LAST";
const SPECIFIC_CHAR = "L";

export default {
  name: "LastCard",
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
      dayOfWeek: null
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
    dayOfWeekMap() {
      return this.mapWeekText(this.dayOfWeek);
    }
  },
  methods: {
    reset() {
      this.active = false;
      this.errorMsg = "";
      this.dayOfWeek = null;
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
      if (this.fieldName === "week") {
        if (this.dayOfWeek !== parseInt(this.dayOfWeek)) {
          this.errorMsg = "The day-of-week value have to be INTEGER";
          return;
        }
        if (this.dayOfWeek < 0 || this.dayOfWeek > 7) {
          this.errorMsg = "The day-of-week value have to be in [0,7]";
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
      if (index === 0) {
        this.handleDayInput();
      } else if (index > 0) {
        this.dayOfWeek = parseInt(this.value.substr(0, index));
      }
      this.validate();
    },
    getCronText() {
      if (this.fieldName === "week") {
        return this.dayOfWeek + SPECIFIC_CHAR;
      } else if (this.fieldName === "day") {
        return SPECIFIC_CHAR;
      }
    },
    getResolvedMeaning() {
      if (this.fieldName === "week") {
        return ` on the last ${this.dayOfWeekMap}`;
      } else if (this.fieldName === "day") {
        return " on last day of the month ";
      }
      const pre = this.resolvedPreText();
      const body = this.resolvedBodyText();
      return pre + body;
    },
    mapWeekText(t) {
      return [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday"
      ][t + ""];
    },
    handleDayInput() {
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
