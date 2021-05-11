<template>
  <div class="cspr-stats max-w-screen-sm m-auto">
    <table class="table-fixed">
      <thead>
        <tr>
          <td colspan="2" class="text-center leading-md">casper-network Statistics <small>(Last Update: {{ lastUpdated }})</small></td>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td class="w-1/2"><strong>Cost of casper-network PoS Validator</strong></td>
          <td class="w-1/2">{{ nodeCost }}</td>
        </tr>
        <tr>
          <td class="w-1/2"><strong>casper-network Circulating Supply</strong></td>
          <td class="w-1/2">{{ csprCirculatingSupply }}</td>
        </tr>
        <tr>
          <td class="w-1/2"><strong>All Time High (ATH)</strong></td>
          <td class="w-1/2">{{ allTimeHigh }}</td>
        </tr>
        <tr>
          <td class="w-1/2"><strong>Percentage from ATH</strong></td>
          <td class="w-1/2">{{ percentageFromAllTimeHigh }}</td>
        </tr>
        <tr>
          <td class="w-1/2"><strong>Days since ATH</strong></td>
          <td class="w-1/2">{{ daysSinceAllTimeHigh }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import dayjs from 'dayjs'
import relativeTime from 'dayjs/plugin/relativeTime'
import { mapGetters } from 'vuex'
import { formatPrice } from '~/libs/utils'

dayjs.extend(relativeTime)

export default {
  computed: {
    ...mapGetters({
      cspr: 'markets/cspr',
      userSelectedCurrency: 'markets/userSelectedCurrency'
    }),
    nodeCost () {
      return this.cspr ? formatPrice(this.cspr.current_price * 32, this.userSelectedCurrency.format, this.userSelectedCurrency.id) : false
    },
    allTimeHigh () {
      return this.cspr ? formatPrice(this.cspr.ath, this.userSelectedCurrency.format, this.userSelectedCurrency.id) : false
    },
    daysSinceAllTimeHigh () {
      if (this.cspr) {
        const athDate = dayjs(this.cspr.ath_date)
        return dayjs().diff(athDate, 'day').toLocaleString(
          this.userSelectedCurrency.format,
          { maximumFractionDigits: 0 }
        )
      }
      return false
    },
    percentageFromAllTimeHigh () {
      return this.cspr ? `${this.cspr.ath_change_percentage.toFixed(2)}%` : false
    },
    csprCirculatingSupply () {
      if (this.cspr) {
        return this.cspr.circulating_supply.toLocaleString(
          this.userSelectedCurrency.format,
          { maximumFractionDigits: 0 }
        )
      }
      return false
    },
    lastUpdated () {
      if (this.cspr) {
        return dayjs(this.cspr.last_updated).fromNow()
      }
      return false
    }
  }
}
</script>

<style lang="scss" scoped>
.cspr-stats {
  width: 100%;

  table {
    @apply bg-white;
    @apply shadow-sm;
    width: 100%;
    border-collapse: collapse;
    overflow: hidden;
    border-radius: 12px;

    .dark & {
      @apply bg-gray-700;
    }
  }

  thead {
    @apply text-lg;
    font-weight: bold;

    td {
      @apply text-gray-900;

      .dark & {
        @apply text-gray-400;
      }
    }

    small {
      display: inline-block;
      position: relative;
      font-size: 0.66em;
      opacity: 0.5;
      margin-left: 1em;
      margin-right: 1em;
      top: -0.25em;
    }
  }

  td {
    @apply border;
    @apply border-blue-100;
    @apply text-blue-900;
    padding: 0.5em 1em;
    font-size: 0.9em;

    .dark & {
      @apply border-gray-900;
      @apply text-gray-300;
    }

    strong {
      font-weight: normal;
      @apply text-gray-800;

      .dark & {
        @apply text-gray-400;
      }
    }
  }
}
</style>
