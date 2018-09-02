<template>
  <div :class="{active: active}">
    <span>past every {{fieldName}} from </span>
    <input type="text" v-model="first" v-on="listeners">
    <span> through </span>
    <input type="text" v-model="second" v-on="listeners">
  </div>
</template>

<script>

const SPECIFIC_CHAR = '-';

export default {
  name: "HyphenCard",
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
      first: null,
      second: null,
    };
  },
  watch: {
    value () {
      if(this.mayResolve()){
        this.resolve();
        if(!this.hasErrorMsg()){
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
      this.first = null;
      this.second = null;
    },
    hasErrorMsg () {
      return !!("string" === typeof this.errorMsg ? this.errorMsg.length : false);
    },
    mayResolve () {
      const result = this.hasSpecificChar(SPECIFIC_CHAR);
      if (result) {
        this.active = true;
      }
      return result;
    },
    validate () {
      if(this.first < this.range[0] || this.first > this.range[1]) {
        this.errorMsg = `The first value have to be in [${this.range[0]},${this.range[1]}]`;
        return;
      }
      if(this.second < this.range[0] || this.second > this.range[1]) {
        this.errorMsg = `The second value have to be in [${this.range[0]},${this.range[1]}]`;
      }
    },
    hasSpecificChar (c) {
      return 'string' === typeof this.value ? this.value.indexOf(c) >= 0: false;
    },
    resolve () {
      if(!this.mayResolve()) {
        return;
      }
      const secondValues = this.value.split(SPECIFIC_CHAR);
      if(2 !== secondValues.length || ''===secondValues[0].trim() || ''===secondValues[1].trim()){
        this.errorMsg = 'This field has the character "-" while can not match "A-B" form.';
        return;
      }
      this.first = secondValues[0];
      this.second = secondValues[1];
      this.validate();
    },
    getCronText () {
      return this.first + SPECIFIC_CHAR + this.second;
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
