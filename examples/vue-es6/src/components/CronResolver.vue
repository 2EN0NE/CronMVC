<template>
  <div>
    <InputText
      v-model="cronText"
      placeholder="Please input the cron style text that you want to resolve"
      @keydown.enter="ensureInput"
    />
    <div class="fields-meaning">
      <FieldResolver field-name="second" v-model="secondText" :allowdSpecialChar="['*',',','-','/']">
        <AsteriskCard field-name="second" :value="secondText" @can-resolve="secondText = $event"/>
        <HyphenCard field-name="second" :value="secondText" @can-resolve="secondText = $event" :range="[0,59]"/>
        <SlashCard field-name="second" :value="secondText" @can-resolve="secondText = $event" :range="[0,59]"/>
        <CommaCard field-name="second" :value="secondText" @can-resolve="secondText = $event" :range="[0,59]"/>
      </FieldResolver>
      <FieldResolver field-name="minute" v-model="minuteText" :allowdSpecialChar="['*',',','-','/']">
        <AsteriskCard field-name="minute" :value="minuteText" @can-resolve="minuteText = $event"/>
        <SlashCard field-name="minute" :value="minuteText" @can-resolve="minuteText = $event" :range="[0,59]"/>
        <HyphenCard field-name="minute" :value="minuteText" @can-resolve="minuteText = $event" :range="[0,59]"/>
        <CommaCard field-name="minute" :value="minuteText" @can-resolve="minuteText = $event" :range="[0,59]"/>
      </FieldResolver>
      <FieldResolver field-name="hour" v-model="hourText" :allowdSpecialChar="['*',',','-','/']">
        <AsteriskCard field-name="hour" :value="hourText" @can-resolve="hourText = $event"/>
        <SlashCard field-name="hour" :value="hourText" @can-resolve="hourText = $event" :range="[0,23]"/>
        <HyphenCard field-name="hour" :value="hourText" @can-resolve="hourText = $event" :range="[0,23]"/>
        <CommaCard field-name="hour" :value="hourText" @can-resolve="hourText = $event" :range="[0,23]"/>
      </FieldResolver>
      <FieldResolver field-name="day" v-model="dayText" :allowdSpecialChar="['*',',','-','/','?','L','W']">
        <AsteriskCard field-name="day" :value="dayText" @can-resolve="dayText = $event"/>
        <SlashCard field-name="day" :value="dayText" @can-resolve="dayText = $event" :range="[0,31]"/>
        <HyphenCard field-name="day" :value="dayText" @can-resolve="dayText = $event" :range="[0,31]"/>
        <CommaCard field-name="day" :value="dayText" @can-resolve="dayText = $event" :range="[0,31]"/>
      </FieldResolver>
      <FieldResolver field-name="month" v-model="monthText" :allowdSpecialChar="['*',',','-']">
        <AsteriskCard field-name="month" :value="monthText" @can-resolve="monthText = $event"/>
        <SlashCard field-name="month" :value="monthText" @can-resolve="monthText = $event" :range="[1,12]"/>
        <HyphenCard field-name="month" :value="monthText" @can-resolve="monthText = $event" :range="[1,12]"/>
        <CommaCard field-name="month" :value="monthText" @can-resolve="monthText = $event" :range="[1,12]"/>
      </FieldResolver>
      <FieldResolver field-name="week" v-model="weekText" :allowdSpecialChar="['*',',','-','?','L','#']">
        <AsteriskCard field-name="week" :value="weekText" @can-resolve="weekText = $event"/>
        <SlashCard field-name="week" :value="weekText" @can-resolve="weekText = $event" :range="[0,6]"/>
        <HyphenCard field-name="week" :value="weekText" @can-resolve="weekText = $event" :range="[0,7]"/>
        <CommaCard field-name="week" :value="weekText" @can-resolve="weekText = $event" :range="[0,7]"/>
      </FieldResolver>
      <FieldResolver field-name="year" v-model="yearText" :allowdSpecialChar="['*',',','-']">
        <AsteriskCard field-name="year" :value="yearText" @can-resolve="yearText = $event"/>
        <SlashCard field-name="year" :value="yearText" @can-resolve="yearText = $event" :range="[1970,2038]"/>
        <HyphenCard field-name="year" :value="yearText" @can-resolve="yearText = $event" :range="[1970,2038]"/>
        <SlashCard field-name="year" :value="yearText" @can-resolve="yearText = $event" :range="[1970,2038]"/>
      </FieldResolver>
    </div>
  </div>
</template>

<script>
import InputText from "./InputText.vue";
import FieldResolver from "./FieldResolver.vue";
import AsteriskCard from "./AsteriskCard.vue";
import SlashCard from "./SlashCard.vue";
import HyphenCard from "./HyphenCard.vue";
import CommaCard from "./CommaCard.vue"

const LINUX_STYLE_SIZE = 6;
const SPRING_STYLE_SIZE = 7;

export default {
  name: "CronResolver",
  components: {
    InputText,
    FieldResolver,
    AsteriskCard,
    SlashCard,
    HyphenCard,
    CommaCard,
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
      cronArraySize: 0
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
      set: function (newValue) {
        const fields = newValue.trim().split(" ");
        this.cronArraySize = fields.length;
        if (this.isLinuxStyle() || this.isSpringStyle()) {
          this.yearText = fields[this.cronArraySize - 1];
          this.weekText = fields[this.cronArraySize - 2];
          this.monthText = fields[this.cronArraySize - 3];
          this.dayText = fields[this.cronArraySize - 4];
          this.hourText = fields[this.cronArraySize - 5];
          this.minuteText = fields[this.cronArraySize - 6];
          this.secondText = this.isSpringStyle() ? fields[this.cronArraySize - 7] : "";
        }
      }
    }
  },
  methods: {
    isLinuxStyle: function () {
      return this.cronArraySize === LINUX_STYLE_SIZE;
    },
    isSpringStyle: function () {
      return this.cronArraySize === SPRING_STYLE_SIZE;
    },
    ensureInput: function() {}
  }
};
</script>

<style lang="scss" scoped>
@import '../../../../public/variables.scss';

h3 {
  margin: 40px 0 0;
}
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
  margin: 1.5ch
}
</style>
