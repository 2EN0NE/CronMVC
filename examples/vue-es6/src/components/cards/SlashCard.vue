<template>
  <div :class="{unactive: !active}">
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
        return "on ";
      } else if (this.fieldName === "hour") {
        return "past";
      } else if (this.fieldName === "minute") {
        return "at ";
      } else if (this.fieldName === "second") {
        return "at ";
      }
    },
    resolvedBodyText() {
      let startText = this.start;
      let stepText = this.step + "th "
      let fieldNameText = this.fieldName;
      if (this.fieldName === "week") {
        startText = this.mapWeekText(startText);
        fieldNameText = "day-of-week";
      } else if (this.fieldName === "month") {
        startText = this.mapMonthText(startText);
        fieldNameText = "day-of-month";
      }
      if("1th "===stepText){stepText = ""}
      if("2th "===stepText){stepText = "2nd "}
      if("3th "===stepText){stepText = "3rd "}
      return `every ${stepText}${fieldNameText} from ${startText}`
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
    mapMonthText(t) {
      return [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ]["" + t];
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
