<template>
  <div :class="{active: active}">
    <span>every {{fieldName}}</span>
  </div>
</template>

<script>

const SPECIFIC_CHAR = '*';

export default {
  name: "AsteriskCard",
  props: {
    fieldName: {
      type: String,
      default: '',
    },
    value: {
      type: String,
      default: '',
    },
  },
  data() {
    return {
      errorMsg: '',
      active: false,
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
  },
  methods: {
    reset () {
      this.active = false;
      this.errorMsg = '';
    },
    hasErrorMsg () {
      return !!("string" === typeof this.errorMsg ? this.errorMsg.length : false);
    },
    mayResolve () {
      return this.hasSpecificChar(SPECIFIC_CHAR);
    },
    validate () {
      if(this.mayResolve() && this.value.length > 1) {
        this.errorMsg = 'This field has more than ONE character besides "*"!'
      }
    },
    hasSpecificChar (c) {
      return 'string' === typeof this.value ? this.value.indexOf(c) >= 0: false;
    },
    resolve () {
      this.validate();
    },
    getCronText () {
      return SPECIFIC_CHAR;
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
