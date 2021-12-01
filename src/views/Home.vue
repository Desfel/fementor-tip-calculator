<template>
  <div class="page-wrapper">
    <img class="logo" src="@/assets/img/logo.svg" />

    <div class="app-wrapper">
      <div class="function-section">
        <div class="input-row">
          <p>Bill</p>

          <div class="input-with-icon">
            <img src="@/assets/img/icon-dollar.svg" alt="Dollar" />
            <input type="text" class="bill-input" v-model="billAmount" placeholder="0" @keyup="calculateBill" />
          </div>
        </div>

        <div class="input-row">
          <p>Select Tip %</p>

          <div class="tip-choices">
            <span :class="{'is-active': activeTip === 5}" @click="chooseTip(5)">5%</span>
            <span :class="{'is-active': activeTip === 10}" @click="chooseTip(10)">10%</span>
            <span :class="{'is-active': activeTip === 15}" @click="chooseTip(15)">15%</span>
            <span :class="{'is-active': activeTip === 20}" @click="chooseTip(20)">20%</span>
            <span :class="{'is-active': activeTip === 50}" @click="chooseTip(50)">50%</span>
            <input type="text" v-model="customTip" @keyup="calculateBill" placeholder="Custom" />
          </div>
        </div>

        <div class="input-row">
          <div class="label-wrapper">
            <p>Number of People</p>
            <p v-if="peopleAmount !== '' && peopleAmount < 1" class="error-message">Can't be zero</p>
          </div>

          <div class="input-with-icon" :class="{'has-error': peopleAmount !== '' && peopleAmount < 1}">
            <img src="@/assets/img/icon-person.svg" alt="Person" />
            <input type="text" class="people-input" v-model="peopleAmount" @keyup="calculateBill" placeholder="0" />
          </div>
        </div>
      </div>
      <div class="display-section">
        <div class="section-row">
          <div class="row-text">
            <p class="row-title">Tip Amount</p>
            <p class="row-subtitle">/ person</p>
          </div>

          <p class="row-amount">$ {{ totalTips }}</p>
        </div>
        <div class="section-row">
          <div class="row-text">
            <p class="row-title">Total</p>
            <p class="row-subtitle">/ person</p>
          </div>

          <p class="row-amount">$ {{ totalSplit }}</p>
        </div>

        <button class="reset-button" @click.prevent="resetCalc">Reset</button>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  data() {
    return {
      billAmount: '',
      customTip: '',
      peopleAmount: '',
      totalSplit: '',
      totalTips: '',
      activeTip: 0
    }
  },
  head() {
    return {
      title: {
        inner: 'Home'
      },
      meta: [
        {
          name: 'description',
          content: `${this.appTitle} home page`,
          id: 'desc'
        }
      ]
    }
  },
  methods: {
    resetCalc() {
      this.billAmount = ''
      this.customTip = ''
      this.peopleAmount = ''
      this.totalSplit = ''
      this.totalTips = ''
    },
    calculateBill() {
      const currentCustomTip = this.customTip.trim()
      const currentBillAmount = this.billAmount.trim()
      const currentPeopleAmount = this.peopleAmount.trim()
      this.totalSplit = 0
      this.totalTips = 0

      if(currentBillAmount !== '' && currentPeopleAmount !== '') {
        if(currentCustomTip !== '') {
          this.activeTip = 0
          this.totalTips = (currentBillAmount * (currentCustomTip / 100)) / currentPeopleAmount
          this.totalSplit = this.totalTips + (currentBillAmount / currentPeopleAmount)
        } else {
          this.customTip = ''
          this.totalTips = (currentBillAmount * (this.activeTip / 100)) / currentPeopleAmount
          this.totalSplit = this.totalTips + (currentBillAmount / currentPeopleAmount)
        }
      }
    },
    chooseTip(value) {
      this.customTip = ''
      this.activeTip = value
      this.calculateBill()
    }
  },
  computed: mapState('app', ['appTitle'])
}
</script>

<style lang="scss" scoped>
@import '@/theme/variables.scss';

.page-wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;

  .logo {
    margin-bottom: 88px;
  }

  .app-wrapper {
    display: flex;
    align-items: stretch;
    width: 100%;
    max-width: 920px;
    padding: 32px 32px 32px 48px;
    background: $white;
    box-shadow: 0px 32px 43px rgba(79, 166, 175, 0.200735);
    border-radius: 25px;

    @media (max-width: 900px) {
      flex-direction: column;
    }

    .function-section {
      display: flex;
      flex-direction: column;

      .input-row {
        &:not(:last-child) {
          margin-bottom: 40px;
        }

        .label-wrapper {
          display: flex;
          align-items: center;
        }

        p {
          font-weight: bold;
          font-size: 16px;
          line-height: 24px;
          color: $neutralColor2;
          margin-bottom: 6px;

          &.error-message {
            margin-left: auto;
            color: $error;
          }
        }

        .input-with-icon {
          display: flex;
          align-items: center;
          background: hsla(185, 38%, 97%, 1);
          border-radius: 5px;
          padding: 6px 17px 6px 20px;

          &.has-error {
            border: 1px solid $error;
          }
        }

        input {
          width: 100%;
          font-family: $textFont;
          text-align: right;
          border: none;
          outline: none;
          font-weight: bold;
          font-size: 24px;
          line-height: 36px;
          color: $neutralColor1;
          background: transparent;
          -webkit-appearance: none;
          margin: 0;

          &::-webkit-inner-spin-button,
          &::-webkit-outer-spin-button {
            opacity: 0;
          }

          &::placeholder {
            opacity: 0.35;
          }
        }

        .tip-choices {
          display: flex;
          flex-wrap: wrap;
          margin: 16px 0 -16px;
          width: 100%;

          span,
          input {
            padding: 14px;
            width: calc((100% - 28px) / 3);
            text-align: center;
            border-radius: 5px;
            font-style: normal;
            font-weight: bold;
            font-size: 24px;
            line-height: 36px;
            margin-bottom: 16px;

            @media (min-width: 1025px) {
              &:not(:nth-child(3)):not(:nth-child(6)) {
                margin-right: 14px;
              }
            }

            @media (max-width: 1024px) {
              width: calc((100% - 14px) / 2);

              &:not(:nth-child(even)) {
                margin-right: 14px;
              }
            }
          }

          span {
            display: inline-block;
            background: $neutralColor1;
            color: $white;
            transition: all .3s ease-in-out;
            cursor: pointer;

            &:hover,
            &.is-active {
              background: $primaryColor;
              color: $neutralColor1;
            }
          }

          input {
            color: $neutralColor1;
            background: hsla(185, 38%, 97%, 1);
            padding: 6px 17px 6px 20px;

            &::placeholder {
              font-size: 22px;
              color: hsla(180, 18%, 40%, 1);
              opacity: 1;
            }
          }
        }
      }
    }

    .display-section {
      display: flex;
      flex-direction: column;
      padding: 40px;
      margin-left: 48px;
      background: $neutralColor1;
      border-radius: 15px;
      width: 100%;
      max-width: 413px;

      @media (max-width: 900px) {
        margin-top: 32px;
        margin-left: 0;
        max-width: 100%;
        padding: 40px 24px 24px;
      }

      .section-row {
        display: flex;
        align-items: center;
        margin-bottom: 25px;

        .row-title {
          font-weight: bold;
          font-size: 16px;
          line-height: 24px;
          color: $white;
        }

        .row-subtitle {
          font-weight: bold;
          font-size: 13px;
          line-height: 19px;
          color: hsla(183, 15%, 56%, 1);
        }

        .row-amount {
          margin-left: auto;
          font-weight: bold;
          font-size: 48px;
          line-height: 71px;
          /* identical to box height */

          text-align: right;
          letter-spacing: -1px;
          color: $primaryColor;

          @media (max-width: 1024px) {
            font-size: 40px;
            line-height: 66px;
            letter-spacing: -0.666667px;
          }

          @media (max-width: 900px) {
            font-size: 32px;
            line-height: 47px;
          }
        }
      }

      .reset-button {
        margin-top: auto;
        padding: 9px 37px;
        border: none;
        width: 100%;
        background: $primaryColor;
        color: $neutralColor1;
        border-radius: 5px;
        font-weight: bold;
        font-size: 20px;
        line-height: 30px;
        text-align: center;
        font-family: $textFont;
      }
    }
  }
}
</style>
