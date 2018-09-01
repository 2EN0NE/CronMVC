<template>
  <div :class="{active: active}">
    <span>past every </span>
    <input type="text" v-model="step" v-on="listeners">
    <span> {{fieldName}} from </span>
    <input type="text" v-model="start" v-on="listeners">
    <span> through {{range[1]}} </span>
  </div>
</template>

<script>

const SPECIFIC_CHAR = '/';

export default {
  name: "SlashCard",
  props: {
    fieldName: {
      type: String,
      default: '',
    },
    value: {
      type: String,
      default: '',
    },
    range: {
      type: Array,
      return: [0, 0],
    }
  },
  data() {
    return {
      errorMsg: '',
      active: false,
      start: null,
      step: null,
    };
  },
  watch: {
    value () {
      if(this.mayResolve()){
        this.resolve();
        if(!this.hasErrorMsg()){
          this.active = true;
          this.$emit('can-resolve', this.getCronText());
        }
      }else{
        this.reset();
      }
      this.$emit('mistakes', this.errorMsg);
    },
  },
  computed: {
    listeners () {
      return {
        ...this.$listeners,
        input: () => this.$emit('can-resolve', this.getCronText())
      }
    },
  },
  methods: {
    reset () {
      this.active = false;
      this.errorMsg = '';
      this.start = null;
      this.step = null;
    },
    hasErrorMsg () {
      return !!("string" === typeof this.errorMsg ? this.errorMsg.length : false);
    },
    mayResolve () {
      return this.hasSpecificChar(SPECIFIC_CHAR);
    },
    validate () {
      if(this.start < this.range[0] || this.start > this.range[1]) {
        this.errorMsg = `The start value have to be in [${this.range[0]},${this.range[1]}]`;
        return;
      }
      if(this.step < this.range[0] + 1 || this.step > this.range[1] + 1) {
        this.errorMsg = `The step value have to be in [${this.range[0] + 1},${this.range[1] + 1}]`;
      }
    },
    hasSpecificChar (c) {
      return 'string' === typeof this.value ? this.value.indexOf(c) >= 0: false;
    },
    resolve () {
      if(!this.mayResolve()) {
        return;
      }
      const stepValues = this.value.split(SPECIFIC_CHAR);
      if(2 !== stepValues.length || ''===stepValues[0].trim() || ''===stepValues[1].trim()){
        this.errorMsg = 'This field has the character "/" while can not match "A/B" form.';
        return;
      }
      this.start = stepValues[0];
      this.step = stepValues[1];
      this.validate();
    },
    getCronText () {
      return this.start + SPECIFIC_CHAR + this.step;
    },
    getResolvedMeaning () {
    },
  }
};
</script>

<style lang="scss" scoped>
@import '../../../../public/variables.scss';

.active {
  color: $warn;
}

</style>
