<template>
  <div>
    <div :class="['d-flex align-center pa-2 c-list', {'textFaint--text': epochHasPassed}]" @click="expandInfo = !expandInfo">
      <div>
        <div class="font-weight-bold">
          ${{ dollarValue }}
        </div>
        <div class="t-small">
          {{ amountHuman }} univ2
        </div>
      </div>
      <v-spacer></v-spacer>
      <div class="text-end">
        <div>
          {{ dateFromNow }}
        </div>
        <div class="t-small">
          {{ dateSimple }}
        </div>
      </div>
      <v-icon class="ml-3" color="textFaint" v-if="epochHasPassed">mdi-lock-open-outline</v-icon>
      <v-icon class="ml-3" color="primary" v-else>mdi-lock</v-icon>
    </div>

    <v-expand-transition>
      <div v-if="expandInfo" class="background px-2 textFaint--text">
        <div>owner: </div>
        <div>ID: </div>
      </div>
    </v-expand-transition>
  </div>
</template>

<script>
import moment from 'moment'
import { ethers } from 'ethers'

export default {
  props: {
    item: {},
    reserveUSD: {
      type: String
    },
    totalSupply: {
      type: String
    }
  },

  data: () => ({
    expandInfo: false
  }),

  computed: {
    percent () {
      if (this.totalSupply === '0') {
        return 0
      }
      var perc = ethers.BigNumber.from(this.item.amount).mul(10000000000).div(this.totalSupply)
      return perc / 10000000000
    },
    dollarValue () {
      var value = this.reserveUSD * this.percent
      return Number(value).toLocaleString(undefined, {minimumFractionDigits: 0, maximumFractionDigits: 2})
    },
    amountHuman () {
      var amount = ethers.utils.formatUnits(this.item.amount, 18)
      return Number(amount).toLocaleString(undefined, {minimumFractionDigits: 0, maximumFractionDigits: 4})
    },
    dateFromNow () {
      if (moment.unix(this.item.unlock_date).isValid()) {
        return moment.unix(this.item.unlock_date).fromNow()
      }
      return ''
    },
    dateSimple () {
      if (moment.unix(this.item.unlock_date).isValid()) {
        return moment.unix(this.item.unlock_date).format('DD/MM/YYYY HH:mm')
      }
      return ''
    },
    epochHasPassed () {
      return this.item.unlock_date < moment().unix()
    }
  }
}
</script>