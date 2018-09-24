<template>
  <div>
    <div class="natural-language">{{naturalLanguage}}</div>
    <InputText
      v-model="cronText"
      placeholder="Please input the cron style text that you want to resolve"
      @keydown.enter="ensureInput"
    />
    <div class="fields-meaning">
      <FieldResolver field-name="second" 
        v-model="secondText" :allowedSpecialChar="['','*',',','-','/']" :defaultRange="[0,59]"
        @resolved="handleResolved"/>
      <FieldResolver field-name="minute" 
        v-model="minuteText" :allowedSpecialChar="['*',',','-','/']" :defaultRange="[0,59]"
        @resolved="handleResolved"/>
      <FieldResolver field-name="hour" 
        v-model="hourText" :allowedSpecialChar="['*',',','-','/']" :defaultRange="[0,23]"
        @resolved="handleResolved"/>
      <FieldResolver field-name="day" 
        v-model="dayText" :allowedSpecialChar="['*',',','-','/','?','L','W']" :defaultRange="[0,31]"
        @resolved="handleResolved"/>
      <FieldResolver field-name="month" 
        v-model="monthText" :allowedSpecialChar="['*',',','-']" :defaultRange="[1,12]"
        @resolved="handleResolved"/>
      <FieldResolver field-name="week" 
        v-model="weekText" :allowedSpecialChar="['*',',','-','?','L','#']" :slashRange="[0,6]" :defaultRange="[0,7]"
        @resolved="handleResolved"/>
      <FieldResolver field-name="year" 
        v-model="yearText" :allowedSpecialChar="['','*',',','-']" :defaultRange="[1970,2038]"
        @resolved="handleResolved"/>
    </div>
  </div>
</template>

<script>
import InputText from "./InputText.vue";
import FieldResolver from "./FieldResolver.vue";

const LINUX_STYLE_SIZE = 5;
const QUARTZ_STYLE_SIZE = 6;
const QUARTZ_WITH_YEAR_STYLE_SIZE = 7;

export default {
  name: "CronResolver",
  components: {
    InputText,
    FieldResolver
  },
  data() {
    return {
      secondText: "",
      minuteText: "",
      hourText: "",
      dayText: "",
      monthText: "",
      weekText: "",
      yearText: "",
      cronArraySize: 0,
      resolvedBus: {
        second: "",
        minute: "",
        hour: "",
        day: "",
        month: "",
        week: "",
        year: ""
      },
      naturalLanguage: ""
    };
  },
  computed: {
    cronText: {
      get: function() {
        return [
          this.secondText,
          this.minuteText,
          this.hourText,
          this.dayText,
          this.monthText,
          this.weekText,
          this.yearText
        ]
          .join(" ")
          .trim();
      },
      set: function(newValue) {
        const fields = this.splitSpace(newValue);
        const cronArraySize = fields.length;
        if (this.isLinuxStyle(newValue) || this.isQuartzStyle(newValue)) {
          this.weekText = fields[cronArraySize - 1];
          this.monthText = fields[cronArraySize - 2];
          this.dayText = fields[cronArraySize - 3];
          this.hourText = fields[cronArraySize - 4];
          this.minuteText = fields[cronArraySize - 5];
          this.secondText = this.isQuartzStyle(newValue)
            ? fields[cronArraySize - 6]
            : "";
        }
        if(this.isQuartzYearStyle(newValue)){
          this.yearText = fields[cronArraySize - 1];
          this.weekText = fields[cronArraySize - 2];
          this.monthText = fields[cronArraySize - 3];
          this.dayText = fields[cronArraySize - 4];
          this.hourText = fields[cronArraySize - 5];
          this.minuteText = fields[cronArraySize - 6];
          this.secondText = fields[cronArraySize - 7];
        }
      }
    }
  },
  watch: {
    cronText() {
      this.updateNaturalLanguage();
    }
  },
  methods: {
    splitSpace(val){
      return val
        .trim()
        .replace(/ +/g, " ")
        .split(" ");
    },
    isLinuxStyle(val) {
      return this.splitSpace(val).length === LINUX_STYLE_SIZE;
    },
    isQuartzStyle(val) {
      return this.splitSpace(val).length === QUARTZ_STYLE_SIZE;
    },
    isQuartzYearStyle(val) {
      return this.splitSpace(val).length === QUARTZ_WITH_YEAR_STYLE_SIZE;
    },
    handleResolved(e) {
      if (e && e.fieldName) {
        this.resolvedBus[e.fieldName] = e.resolved;
      }
      this.updateNaturalLanguage();
    },
    updateNaturalLanguage() {
      if (
        !this.isLinuxStyle(this.cronText) &&
        !this.isQuartzStyle(this.cronText) &&
        !this.isQuartzYearStyle(this.cronText)
      ) {
        this.naturalLanguage = "";
        return;
      }
      const t = [];
      for (let name in this.resolvedBus) {
        if (this.resolvedBus[name]) {
          t.push(this.resolvedBus[name]);
        }
      }
      this.naturalLanguage = `“${t.join(", ")}”`;
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../public/variables.scss";

ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: $vue-green;
}
.fields-meaning {
  margin: 1.5ch;
}
.natural-language {
  margin: 2ch;
  font-size: 2ch;
  color: grey;
}
</style>
