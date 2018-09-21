<template>
  <div class="field-resolver row" >
    <div class="separated-text-box col-2">
      <div class="field-name">
        <span>{{fieldName}}</span>
      </div>
      <input class="separated-text" type="text" :value="value" v-on="listeners">
    </div>
    <div class="cards-box col-9" :class="{'cards-box-active':allCardsDisplayed}">
      <!-- <EmptyCard /> -->
      <AsteriskCard class="card" v-if="allowCard('*')" v-show="isSelected('ASTERISK')" @click.native="chooseThisCard('ASTERISK')"
        :field-name="fieldName" :value="cronText" @resolved="handleResolved" @update:card="cardUpdate"/>
      <SlashCard class="card" v-if="allowCard('/')" v-show="isSelected('SLASH')" @click.native="chooseThisCard('SLASH')"
        :field-name="fieldName" :value="cronText" :range="slashRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate"/>
      <HyphenCard class="card" v-if="allowCard('-')" v-show="isSelected('HYPHEN')" @click.native="chooseThisCard('HYPHEN')"
       :field-name="fieldName" :value="cronText" :range="hyphenRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate"/>
      <CommaCard class="card" v-if="allowCard(',')" v-show="isSelected('COMMA')" @click.native="chooseThisCard('COMMA')"
       :field-name="fieldName" :value="cronText" :range="commaRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate"/>
      <!-- <QuestionCard /> -->
      <!-- <LastCard /> -->
      <!-- <WeekdayCard /> -->
      <!-- <HashCard /> -->
    </div>
    <div class="select-btn col-1" v-on:click="showAllCards">选择</div>
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
    },
    allCardsDisplayed() {
      for (const name in this.cards) {
        if (!this.cards[name].active) {
          return false;
        }
      }
      return true;
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
        for (const field in this.cards) {
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
      this.$emit("input", e);
    },
    isOtherCardsActive() {},
    resetCards() {
      this.cards = {
        ASTERISK: { active: false, resolved: "", errorMsg: "" },
        COMMA: { active: false, resolved: "", errorMsg: "" },
        HYPHEN: { active: false, resolved: "", errorMsg: "" },
        SLASH: { active: false, resolved: "", errorMsg: "" },
        QUESTION: { active: false, resolved: "", errorMsg: "" },
        LAST: { active: false, resolved: "", errorMsg: "" },
        WEEKDAY: { active: false, resolved: "", errorMsg: "" },
        HASH: { active: false, resolved: "", errorMsg: "" }
      };
    },
    showAllCards() {
      if(this.allCardsDisplayed){
        this.resetCards();
      }else{
        for (let cardName in this.cards) {
          this.cards[cardName].active = true;
        }
      }
    },
    chooseThisCard(cardName) {
      this.resetCards();
      this.cards[cardName].active = true;
    },
    isSelected(cardName) {
      return this.cards[cardName].active;
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../public/variables.scss";

.field-resolver {
  margin: 0.5ch;
  border-bottom: 1px solid #ebebeb;
  padding: 10px 5px 10px;
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
  display: flex;
}
.separated-text-box {
  line-height: 39.9999px;
  overflow: hidden;
  white-space: nowrap;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding-right: 5px;
  border-right: 1px solid #f4f4f4;
}
.cards-box {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.cards-box-active {
  z-index: 10;
}
.cards-box-background {
  background-color: grey;
  z-index: 1;
}
.meaning {
  color: $vue-green;
}
.error {
  color: $warn;
}
.cards-box-active .card:hover {
  border: 1px solid #f0f0f0;
  border-radius: 3px;
  box-shadow:0px 0px 10px #dddddd;
}
.select-btn {
  display: flex;
  flex-direction: column;
  justify-content: center;
  border-left: 1px solid #f4f4f4;
}
.field-name {
}
.separated-text {
  display: block;
  width: 90%;
}
</style>
