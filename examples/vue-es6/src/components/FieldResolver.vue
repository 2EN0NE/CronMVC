<template>
  <div class="field-resolver row" >
    <div class="separated-text-box col-2">
      <div class="field-name">
        <span>{{fieldName}}</span>
      </div>
      <input class="separated-text" type="text" :value="value" v-on="listeners">
    </div>
    <div class="cards-box col-9" :class="{'cards-box-display':allCardsDisplayed}">
      <!-- <EmptyCard /> -->
      <AsteriskCard id="ASTERISK" class="card" :class="{'card-seperate':allCardsDisplayed&&hasNextEle('ASTERISK')}"
        v-if="allowCard('*')" v-show="isSelected('ASTERISK')"
        :field-name="fieldName" :value="cronText"
        @resolved="handleResolved" @update:card="cardUpdate" @error="errorMsg=$event"
        @click.native="chooseThisCard('ASTERISK')"/>
      <SlashCard id="SLASH" class="card" :class="{'card-seperate':allCardsDisplayed&&hasNextEle('SLASH')}"
        v-if="allowCard('/')" v-show="isSelected('SLASH')"
        :field-name="fieldName" :value="cronText" :range="slashRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate" @error="errorMsg=$event"
        @click.native="chooseThisCard('SLASH')"/>
      <HyphenCard id="HYPHEN" class="card" :class="{'card-seperate':allCardsDisplayed&&hasNextEle('HYPHEN')}"
        v-if="allowCard('-')" v-show="isSelected('HYPHEN')" 
        :field-name="fieldName" :value="cronText" :range="hyphenRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate" @error="errorMsg=$event"
        @click.native="chooseThisCard('HYPHEN')"/>
      <CommaCard id="COMMA" class="card" :class="{'card-seperate':allCardsDisplayed&&hasNextEle('COMMA')}"
        v-if="allowCard(',')" v-show="isSelected('COMMA')" 
        :field-name="fieldName" :value="cronText" :range="commaRange||defaultRange"
        @resolved="handleResolved" @update:card="cardUpdate" @error="errorMsg=$event"
        @click.native="chooseThisCard('COMMA')"/>
      <!-- <QuestionCard /> -->
      <!-- <LastCard /> -->
      <!-- <WeekdayCard /> -->
      <!-- <HashCard /> -->
      <div class="error-message" v-if="errorMsg">{{errorMsg}}</div>
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
        ASTERISK: { display: false, resolved: "", errorMsg: "" },
        COMMA: { display: false, resolved: "", errorMsg: "" },
        HYPHEN: { display: false, resolved: "", errorMsg: "" },
        SLASH: { display: false, resolved: "", errorMsg: "" },
        QUESTION: { display: false, resolved: "", errorMsg: "" },
        LAST: { display: false, resolved: "", errorMsg: "" },
        WEEKDAY: { display: false, resolved: "", errorMsg: "" },
        HASH: { display: false, resolved: "", errorMsg: "" }
      },
      lastDisplayed: ""
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
        if (!this.cards[name].display) {
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
    allowCard(t) {
      return ~this.allowedSpecialChar.indexOf(t);
    },
    handleResolved(e) {
      if (e && e.cardName && this.cards[e.cardName]) {
        this.resetCards();
        this.cards[e.cardName].display = true;
        this.cards[e.cardName].resolved = e.resolved;
        this.lastDisplayed = e.cardName;
        this.errorMsg = "";
      }
    },
    cardUpdate(e) {
      if (e && e.cardName && e.cronText) {
        this.$emit("input", e.cronText);
        this.lastDisplayed = e.cardName;
      }
    },
    resetCards() {
      this.cards = {
        ASTERISK: { display: false, resolved: "", errorMsg: "" },
        COMMA: { display: false, resolved: "", errorMsg: "" },
        HYPHEN: { display: false, resolved: "", errorMsg: "" },
        SLASH: { display: false, resolved: "", errorMsg: "" },
        QUESTION: { display: false, resolved: "", errorMsg: "" },
        LAST: { display: false, resolved: "", errorMsg: "" },
        WEEKDAY: { display: false, resolved: "", errorMsg: "" },
        HASH: { display: false, resolved: "", errorMsg: "" }
      };
    },
    showAllCards() {
      if (this.allCardsDisplayed) {
        this.resetCards();
        if (this.lastDisplayed) {
          this.cards[this.lastDisplayed].display = true;
        }
      } else {
        for (let cardName in this.cards) {
          this.cards[cardName].display = true;
        }
      }
    },
    chooseThisCard(cardName) {
      this.resetCards();
      this.cards[cardName].display = true;
      this.lastDisplayed = cardName;
    },
    isSelected(cardName) {
      return this.cards[cardName].display;
    },
    hasNextEle(cardName) {
      const ele = document.getElementById(cardName);
      if (ele) {
        return !(ele.parentElement.lastElementChild === ele);
      } else {
        return false;
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../public/variables.scss";

.field-resolver {
  margin: 0.5ch;
  border-bottom: 1px solid $grey2;
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
  padding: 3px 10px 3px 10px;
  border-right: 1px solid $grey1;
}
.cards-box {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.cards-box-display {
  z-index: 10;
}
.cards-box-background {
  background-color: grey;
  z-index: 1;
}
.meaning {
  color: $vue-green;
}
.error-message {
  border-top: 1px solid $grey1;
  color: $warn;
}
.cards-box-display .card:hover {
  border: 1px solid $grey1;
  border-radius: 3px;
  box-shadow: 0px 0px 10px $grey3;
}
.select-btn {
  cursor: pointer;
  display: flex;
  flex-direction: column;
  justify-content: center;
  border-left: 1px solid $grey1;
}
.field-name {
}
.separated-text {
  display: block;
  width: 100%;
}
.card {
  padding: 5px;
}
.card-seperate {
  border-bottom: 1px solid $grey1;
}
</style>
