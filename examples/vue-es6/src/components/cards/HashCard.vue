<template>
  <div :class="{unactive: !active}">
    <span> the
       {{xthMap}}(<input type="text" v-model="xth" v-on="listeners" style="width:3ch">)
        {{dayOfWeekMap}}(<input type="text" v-model="dayOfWeek" v-on="listeners" style="width:3ch">)
         of every month. </span>
  </div>
</template>

<script>
const cardName = "HASH";
const SPECIFIC_CHAR = "#";

export default {
  name: "HashCard",
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
      xth: null,
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
    },
    dayOfWeekMap() {
      return this.mapWeekText(this.dayOfWeek);
    },
    xthMap() {
      return ["", "first", "second", "third", "4th"][this.xth];
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
      if (this.dayOfWeek < 1 || this.step > 5) {
        this.errorMsg = "The first value have to be in [1,5]";
        return;
      }
      if (this.xth < 1 || this.start > 4) {
        this.errorMsg = "The second value have to be in [1,4]";
        return;
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
          'This field has the character "#" while can not match "A#B" form.';
        return;
      }
      this.dayOfWeek = parseInt(stepValues[0]);
      this.xth = parseInt(stepValues[1]);
      this.validate();
    },
    getCronText() {
      const dayOfWeek = this.dayOfWeek ? this.dayOfWeek : "";
      const xth = this.xth ? this.xth : "";
      return dayOfWeek + SPECIFIC_CHAR + xth;
    },
    getResolvedMeaning() {
      return ` on the ${this.xthMap} ${this.dayOfWeekMap} of every month `;
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
