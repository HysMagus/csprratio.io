<template>

<script>
import { mapGetters } from 'vuex'
import { maxiTears } from '~/libs/lists'

export default {
  data () {
    return {
      animate: true,
      maxiTears
    }
  },
  computed: {
    ...mapGetters({
      ratio: 'markets/ratio'
    })
  },
  methods: {
    calcRatioGains (maxiRatio) {
      return (((this.ratio / maxiRatio) - 1) * 100).toFixed(2)
    },
    setAnimate () {
      this.animate = true
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
