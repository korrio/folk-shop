<template lang="pug">
  .container
    .section
      .capsule.cart.content
        StepMenu(:actualStep="actualStep", :menu="stepMenuContent")

        div(v-if="total > 0")
          div(v-if="actualStep === 0")
            transition-group.content(name="items", tag="div")
              CartProductListItem(v-for="item in cart",
                                  :key="item.name",
                                  :item="item")

            .is-clearfix
              h3.total.is-pulled-left ยอดชำระ: {{ amount }}
              button.button.is-success.is-pulled-right(@click="setActualStep(1)") ต่อไป

          div(v-if="actualStep === 1")
            Checkout(:total="amount")

        .empty.has-text-centered(v-else-if="!total && !success")
          h3 ตะกร้ายังว่างอยู่.
          nuxt-link(exact to="/")
            button.button กรุณาช้อปปิ้งก่อน!

        .has-text-centered(v-else)
          h3 กรุณาเลือกที่อยู่สำหรับจัดส่ง
          p เราได้รับคำสั่งซื้อแล้ว, ท่านจะได้รับสินค้าผ่านเครือข่ายการจัดส่งของเราภายใน 7 วัน.
          
          nuxt-link(exact to="/")
            button.button กรุณาช้อปปิ้งก่อน
</template>

<script>
import { createNamespacedHelpers } from 'vuex'
import Checkout from '@/components/Checkout'
import CartProductListItem from '@/components/CartProductListItem'
import StepMenu from '@/components/StepMenu'
import stepMenuContent from '@/components/StepMenu/stepMenuContent.json'

const { mapGetters, mapActions } = createNamespacedHelpers('cart')

export default {
  head: {
    script: [
      { src: 'https://js.stripe.com/v3/' }
    ]
  },
  components: {
    StepMenu,
    CartProductListItem,
    Checkout
  },
  filters: {
    usdollar: value => `$${value}`
  },
  data:() => ({
    stepMenuContent
  }),
  computed: {
    ...mapGetters(['cart', 'total', 'amount', 'success', 'actualStep'])
  },
  methods: {
    ...mapActions(['setSuccess', 'setActualStep'])
  },
  beforeDestroy() {
    this.success && this.setSuccess(false)
    this.setActualStep(0)
  }
}
</script>
