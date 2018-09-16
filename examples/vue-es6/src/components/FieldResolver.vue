<template>
  <div class="field-resolver row">
    <div class="separated-text col-3">
      <span>{{fieldName}} : </span>
      <input
      type="text"
      :value="value"
      v-on="listeners">
    </div>
    <div class="meaning-cards col-9">
      <AsteriskCard v-if="allowCard('*')" :field-name="fieldName" :value="cronText" @resolved="handleResolved"/>
      <SlashCard v-if="allowCard('/')" :field-name="fieldName" :value="cronText" :range="slashRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate"/>
      <HyphenCard v-if="allowCard('-')" :field-name="fieldName" :value="cronText" :range="hyphenRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate"/>
      <CommaCard v-if="allowCard(',')" :field-name="fieldName" :value="cronText" :range="commaRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate"/>
    </div>
  </div>
</template>

<script>
import AsteriskCard from "./cards/AsteriskCard.vue";
import CommaCard from "./cards/CommaCard.vue";
import HyphenCard from "./cards/HyphenCard.vue";
import SlashCard from "./cards/SlashCard.vue";

const ASTERISK = "*";
const COMMA = ",";
const HYPHEN = "-";
const SLASH = "/";
const QUESTION = "?";
const LAST = "L";
const WEEKDAY = "W";
const HASH = "#";

export default {
  name: "FieldResolver",
  components: {
    AsteriskCard,
    SlashCard,
    HyphenCard,
    CommaCard
  },
  props: {
    fieldName: {
      type: String,
      default: ""
    },
    value: {
      type: String,
      default: ""
    },
    allowedSpecialChar: {
      type: Array,
      return: [ASTERISK, COMMA, HYPHEN, SLASH, QUESTION, LAST, WEEKDAY, HASH]
    },
    defaultRange: {
      type: Array,
      return: []
    },
    slashRange: null,
    hyphenRange: null,
    commaRange: null
  },
  data() {
    return {
      errorMsg: "",
      cronText: this.value || "",
      cards: {
        ASTERISK: { active: false, resolved: "", errorMsg: "" },
        COMMA: { active: false, resolved: "", errorMsg: "" },
        HYPHEN: { active: false, resolved: "", errorMsg: "" },
        SLASH: { active: false, resolved: "", errorMsg: "" },
        QUESTION: { active: false, resolved: "", errorMsg: "" },
        LAST: { active: false, resolved: "", errorMsg: "" },
        WEEKDAY: { active: false, resolved: "", errorMsg: "" },
        HASH: { active: false, resolved: "", errorMsg: "" }
      }
    };
  },
  computed: {
    listeners() {
      return {
        ...this.$listeners,
        input: event => this.$emit("input", event.target.value)
      };
    },
    validSpecialCharInText() {
      return [
        ASTERISK,
        COMMA,
        HYPHEN,
        SLASH,
        QUESTION,
        LAST,
        WEEKDAY,
        HASH
      ].filter(text => ~this.value.indexOf(text));
    }
  },
  watch: {
    value() {
      this.cronText = this.value;
    },
    cards: {
      handler(newVal, oldVal) {},
      deep: true
    }
  },
  methods: {
    hasErrorMsg() {
      return !!("string" === typeof this.errorMsg
        ? this.errorMsg.length
        : false);
    },
    // hasAnySpecificChar() {
    //   return !!this.value.match(/\D/g);
    // },
    allowCard(t) {
      return ~this.allowedSpecialChar.indexOf(t);
    },
    activateCard(e) {
      if (e && e.cardName && this.cards[e.cardName]) {
        for (let field in this.cards) {
          this.cards[field].active = field === e.cardName ? true : false;
        }
      }
    },
    handleResolved(e) {
      // if (e && e.cardName && this.cards[e.cardName]) {
      //   this.cards[e.cardName].resolved = e.resolved;
      // }
    },
    cardUpdate(e) {
      // if (e && e.cardName) {
      //   if (this.isOtherCardsActive(e.cardName)) {
      //     this.resetCards();
      //   }
      //   if (e.cronText) {
      //   }
      // }
      this.$emit("input", e)
    },
    isOtherCardsActive() {},
    resetCards() {}
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../public/variables.scss";

.field-resolver {
  margin: 0.5ch;
  border-bottom: 1px solid #ebedf0;
  padding: 21px 12px 25px;
  color: rgba(0, 0, 0, 0.65);
  font-family: "Chinese Quote", -apple-system, BlinkMacSystemFont, "Segoe UI",
    "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", "Helvetica Neue",
    Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji",
    "Segoe UI Symbol";
  font-size: 14px;
  font-variant: tabular-nums;
  line-height: 1.5;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  list-style: none;
  vertical-align: top;
}
.separated-text {
  text-align: right;
  vertical-align: middle;
  line-height: 39.9999px;
  display: inline-block;
  overflow: hidden;
  white-space: nowrap;
}
.meaning-cards {
}
.meaning {
  color: $vue-green;
}
.error {
  color: $warn;
}
</style>
