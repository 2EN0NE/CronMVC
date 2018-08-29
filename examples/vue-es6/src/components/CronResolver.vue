<template>
  <div>
    <InputText
      v-model="cronText"
      placeholder="Please input the cron style text that you want to resolve"
      @keydown.enter="ensureInput"
    />
    <div class="fields-meaning">
      <FieldResolver field-name="second" v-model="secondText" :allowdSpecialChar="['*',',','-','/']"/>
      <FieldResolver field-name="minute" v-model="minuteText" :allowdSpecialChar="['*',',','-','/']"/>
      <FieldResolver field-name="hour" v-model="hourText" :allowdSpecialChar="['*',',','-','/']"/>
      <FieldResolver field-name="day" v-model="dayText" :allowdSpecialChar="['*',',','-','/','?','L','W']"/>
      <FieldResolver field-name="month" v-model="monthText" :allowdSpecialChar="['*',',','-']"/>
      <FieldResolver field-name="week" v-model="weekText" :allowdSpecialChar="['*',',','-','?','L','#']"/>
      <FieldResolver field-name="year" v-model="yearText" :allowdSpecialChar="['*',',','-']"/>
    </div>
  </div>
</template>

<script>
import InputText from "./InputText.vue";
import FieldResolver from "./FieldResolver.vue";

const LINUX_STYLE_SIZE = 6;
const SPRING_STYLE_SIZE = 7;

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
