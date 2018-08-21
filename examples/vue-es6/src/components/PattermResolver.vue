<template>
  <div>
    <span>{{fieldName}}</span>
    <input
    type="text"
    :value="value"
    v-on="listeners">
    <!-- <MeaningCard :every="fieldName"> -->
  </div>
</template>

<script>
export default {
  name: "PattermResolver",
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
    return {};
  },
  computed: {
    listeners () {
      return {
        ...this.$listeners,
        input: event => this.$emit('input', event.target.value)
      }
    }
  },
  methods: {
    resolve: function() {}
  }
};
</script>

