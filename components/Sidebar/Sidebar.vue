<template lang="pug">
  aside.is-light.is-radius
    .sidearea
      label.subtitle.is-5(for="category") หมวดหมู่
      .select
        select#category(@input="setCategory($event.target.value)")
          option(v-for="category in categories",
            :key="category",
            :selected="category === categorySelected",
            :value="category") {{ category }}
</template>

<script>
import { createNamespacedHelpers } from 'vuex'
import Switch from '@/components/Switch'

const { mapActions, mapGetters } = createNamespacedHelpers('product')

export default {
  name: 'Sidebar',
  components: {
    AppSwitch: Switch
  },
  props: {
    sale: {
      type: Boolean,
      default: false
    },
    pricerange: {
      type: [Number, String],
      default: 300
    }
  },
  data() {
    return {
      min: 0,
      max: 400
    }
  },
  methods: {
    ...mapActions(['updateHighprice', 'setCategory'])
  },
  computed: {
    ...mapGetters(['categories', 'categorySelected'])
  }
}
</script>

<style lang="stylus" scoped>
  aside
    float left
    width 19.1489%
    padding 1.5rem
    position sticky

  .sidearea
    border-bottom 1px solid #ccc
    padding 20px 0

    &:first-of-type
      padding-top 0
      padding-bottom 40px

    &:last-of-type
      border none
      padding-bottom 0

    .subtitle
      padding-bottom 10px
      margin-bottom 0
      display block

  span
    font-family 'Barlow', sans-serif

  .min,
  .max
    font-size 12px
    color #565656
</style>
