<template>
  <div class="field-resolver">
    <span>{{fieldName}} : </span>
    <input
    type="text"
    :value="value"
    v-on="listeners">
    <span class="meaning" v-if="resolvedMeaning"> -- {{resolvedMeaning}}</span>
    <slot></slot>
  </div>
</template>

<script>

const ASTERISK = "*";
const COMMA = ",";
const HYPHEN = "-";
const SLASH = "/";
const QUESTION = "?"
const LAST = "L";
const WEEKDAY = "W";
const HASH = "#";

export default {
  name: "FieldResolver",
  props: {
    fieldName: {
      type: String,
      default: ""
    },
    value: {
      type: String,
      default: ""
    },
    allowdSpecialChar: {
      type: Array,
      return: [
        ASTERISK,
        COMMA,
        HYPHEN,
        SLASH,
        QUESTION,
        LAST,
        WEEKDAY,
        HASH,
      ]
    }
  },
  data() {
    return {
      errorMsg: "",
    };
  },
  computed: {
    listeners () {
      return {
        ...this.$listeners,
        input: event => this.$emit('input', event.target.value)
      }
    },
    validSpecialCharInText () {
      return [
        ASTERISK,
        COMMA,
        HYPHEN,
        SLASH,
        QUESTION,
        LAST,
        WEEKDAY,
        HASH,
      ].filter(text => this.value.indexOf(text) + 1)
    },
    resolvedMeaning () {
      this.checkField();
      if(!this.hasErrorMsg()){
        if(this.hasSpecificChar(ASTERISK)){
          return this.resolveAsterrisk();
        }
        if(this.hasSpecificChar(HYPHEN)){
          return this.resolveHyphen();
        }
        if(this.hasSpecificChar(SLASH)){
          return this.resolveSlash();
        }
        return this.resolveComma();
      }
      return ''
    }
  },
  methods: {
    hasErrorMsg () {
      return !!("string" === typeof this.errorMsg ? this.errorMsg.length : false);
    },
    hasAnySpecificChar () {
      return !!this.value.match(/\D/g);
    },
    checkField: function () {
      // TODO isSpecificCharValid, isInRange => setErrorMsg
      if(this.hasAnySpecificChar()){
        if(0 === this.validSpecialCharInText.length) {
          this.errorMsg = 'The text has the character that can not used in cron expression. Make sure the specific character in the set of ["*", ",", "-", "/", "?", "L", "W", "#"]'
        }else if (this.validSpecialCharInText.length > 1){
          this.errorMsg = 'There are more than ONE specific character, system can not resolve!'
        }
      }
    },
    hasSpecificChar: function (c) {
      return "string" === typeof this.value ? this.value.indexOf(c) >= 0: false;
    },
    resolveAsterrisk: function () {
      return `every ${this.fieldName}s`; 
    },
    resolveComma: function () {
      return `${this.value} ${this.value?this.fieldName:''}`.trim();
    },
    resolveHyphen: function () {
      // TODO now is the simplest.
      const range = this.value.split('-');
      if(2 !== range.length){
        this.errorMsg = 'There are something wrong while resolve hyphen character.'
      }
      return `range from ${range[0]} to ${range[1]} ${this.fieldName}`;
    },
    resolveSlash: function () {
      // TODO now is the simplest.
      const step = this.value.split('/');
      if(2 !== step.length){
        this.errorMsg = 'There are something wrong while resolve slash character.'
      }
      return `starting from the ${step[0]} ${this.fieldName}, and excute every ${step[1]} ${this.fieldName}`;
    },
    hasStep: function (foo) {
      return "string" == typeof foo ? foo.indexOf(SLASH) >= 0 : false;
    },
    hasStartUpTime: function (foo) {
      return "string" == typeof foo ? foo.indexOf(QUESTION) >= 0 : false;
    },
    hasLast: function (foo) {
      return "string" == typeof foo ? foo.indexOf(LAST) >= 0 : false;
    },
    hasWeekDay: function (foo) {
      return "string" == typeof foo ? foo.indexOf(WEEKDAY) >= 0 : false;
    },
    hasDayOfWeek: function (foo) {
      return "string" == typeof foo ? foo.indexOf(HASH) >= 0 : false;
    },

    resolve: function() {}
  }
};
</script>

<style lang="scss" scoped>
@import '../../../../public/variables.scss';

.field-resolver {
  margin: 0.5ch;
}
.meaning {
  color: $vue-green;
}
.error {
  color: $warn;
}

</style>
