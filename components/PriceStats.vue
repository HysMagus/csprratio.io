<template>
  <div
    :key="updateTime"
    v-observe.once.-100px="setAnimate"
    class="price-stats pb-1 flex flex-wrap align-top justify-center text-center text-gray-600 dark:text-gray-500 leading-tight"
    :data-animate="animate"
    :data-is-sticky="sticky"
  >
    <div
      v-observe="setSticky"
      class="sticky-trigger"
    />
    <span class="text-right mr-2 sm:mr-4 md:mr-8">
      <strong class="text-gray-700 dark:text-gray-400">CSPR:</strong> <span class="monospace">{{ formatPrice(CSPR.current_price, userSelectedCurrency.format, userSelectedCurrency.id) }}</span><br>
      <span class="change-percent" :data-increased="isPossitive(CSPR24hPercentChange)">
        {{ CSPR24hPercentChange }}% <span class="text-gray-600 dark:text-gray-500">24h price</span>
      </span>
    </span>
    <span class="text-right mr-2 sm:mr-4 md:mr-8">
      <strong class="text-gray-700 dark:text-gray-400">ETH:</strong> <span class="monospace">{{ formatPrice(ETH.current_price, userSelectedCurrency.format, userSelectedCurrency.id) }}</span><br>
      <span class="change-percent" :data-increased="isPossitive(ETH24hPercentChange)">
        {{ ETH24hPercentChange }}% <span class="text-gray-600 dark:text-gray-500">24h price</span>
      </span>
    </span>
    <span class="text-right">
      <strong class="text-gray-700 dark:text-gray-400">Ratio:</strong> <span class="monospace">{{ ratio }}</span><br>
      <span class="change-percent" :data-increased="isPossitive(ratioChange24h)">
        {{ ratioChange24h }}% <span class="text-gray-600 dark:text-gray-500">24h change</span>
      </span>
    </span>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { formatPrice } from '~/libs/utils'

export default {
  data () {
    return {
      updateTime: null,
      animate: true,
      sticky: false
    }
  },
  computed: {
    ...mapGetters({
      userSelectedCurrency: 'markets/userSelectedCurrency',
      CSPR: 'markets/CSPR',
      ETH: 'markets/ETH',
      ratio: 'markets/ratio',
      CSPR24hPercentChange: 'markets/CSPR24hPercentChange',
      ETH24hPercentChange: 'markets/ETH24hPercentChange'
    }),
    ratioChange24h () {
      if (this.CSPR && this.ETH && this.ratio) {
        const oldCSPRPrice = parseFloat(this.CSPR.current_price) + parseFloat(this.CSPR.price_change_24h)
        const oldETHPrice = parseFloat(this.ETH.current_price) + parseFloat(this.ETH.price_change_24h)
        const oldRatio = parseFloat((oldCSPRPrice / oldETHPrice).toFixed(6))
        const ratioChange = (((oldRatio - parseFloat(this.ratio)) / oldRatio) * 100).toFixed(2)
        return ratioChange
      }
      return false
    }
  },
  mounted () {
    this.updateTime = new Date().getTime()
    setInterval(() => {
      this.updateTime = new Date().getTime()
    }, 60000)
  },
  mCSPRods: {
    formatPrice,
    isPossitive (number) {
      return number >= 0 ? 'yes' : 'no'
    },
    setAnimate () {
      this.animate = true
    },
    setSticky (e) {
      this.sticky = !e
    }
  }
}
</script>

<style lang="scss" scoped>
.price-stats {
  position: sticky;
  top: -1px;
  margin-bottom: 1em;
  transform: translateY(-1em);
  opacity: 0;
  transition: opacity 0.33s ease-out, transform 0.33s ease-out, box-shadow 0.33s ease-out;
  z-index: 5;
  font-size: 0.66em;

  @media (min-width: 375px) {
    font-size: 0.8em;
  }
  @media (min-width: 420px) {
    font-size: 0.85em;
  }

  &[data-animate="true"] {
    transform: translateY(0);
    opacity: 1;
  }

  &[data-is-sticky="true"] {
    @apply border-b;
    @apply border-blue-100;
    @apply bg-blue-50;
    @apply dark:bg-gray-800;
    @apply dark:border-gray-900;
    box-shadow: 0 0 1em 0 rgba(#000, 0.075);
  }

  .sticky-trigger {
    position: absolute;
    top: 0px;
    width: 100%;
  }

  & > span {
    display: inline-block;
    margin-bottom: 0.25em;
    margin-top: 0.5em;

    &:last-child {
      margin-right: 0;
      margin-bottom: 0;
    }
  }

  .change-percent {
    font-size: 0.85em;

    &[data-increased="no"] {
      @apply text-red-500;
    }
    &[data-increased="yes"] {
      @apply text-green-500;
    }
  }
}
</style>
