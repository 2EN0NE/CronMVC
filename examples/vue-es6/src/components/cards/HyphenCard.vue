<template>
  <div :class="{unactive: !active}">
    <span>past every {{fieldName}} from </span>
    <input type="text" v-model="start" v-on="listeners">
    <span> through </span>
    <input type="text" v-model="end" v-on="listeners">
  </div>
</template>

<script>
const cardName = "HYPHEN";
const SPECIFIC_CHAR = "-";

export default {
  name: "HyphenCard",
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
      end: null
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
    }
  },
  methods: {
    reset() {
      this.active = false;
      this.errorMsg = "";
      this.start = null;
      this.end = null;
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
        this.errorMsg = `The first value have to be in [${this.range[0]},${
          this.range[1]
        }]`;
        return;
      }
      if (this.end < this.range[0] || this.end > this.range[1]) {
        this.errorMsg = `The second value have to be in [${this.range[0]},${
          this.range[1]
        }]`;
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
      const splitValues = this.value.split(SPECIFIC_CHAR);
      if (
        2 !== splitValues.length ||
        "" === splitValues[0].trim() ||
        "" === splitValues[1].trim()
      ) {
        this.errorMsg =
          'This field has the character "-" while can not match "A-B" form.';
        return;
      }
      this.start = parseInt(splitValues[0]);
      this.end = parseInt(splitValues[1]);
      this.validate();
    },
    getCronText() {
      const start = this.start ? this.start : "";
      const end = this.end ? this.end : "";
      return start + SPECIFIC_CHAR + end;
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
      let endText = this.end
      let fieldNameText = this.fieldName;
      if (this.fieldName === "week") {
        startText = this.mapWeekText(startText);
        fieldNameText = "day-of-week";
      } else if (this.fieldName === "month") {
        startText = this.mapMonthText(startText);
        fieldNameText = "day-of-month";
      }
      return `every ${fieldNameText} from ${startText} through ${endText}`
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
