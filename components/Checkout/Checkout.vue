<template lang="pug">
  .content
    transition(name="fade")

      form.payment(v-if="status !== 'failure'", @submit.prevent="beforePay")
        h3 กรุณาใส่เลขบัตรเครดิตหรือบัตรเดบิต:

        .field
          label.label(for="email") Email
          .control.has-icons-left.has-icons-right
            input.input#email(type="email",
                              required,
                              v-model="userEmail",
                              placeholder="name@example.com",
                              name="email",
                              v-validate="'required|email'",
                              :class="{ 'is-danger': errors.has('email') }")
            span.icon.is-small.is-left
              i.fa.fa-envelope
            span.icon.is-small.is-right(v-if="errors.has('email')")
              i.fa.fa-exclamation-triangle
            p.help.is-danger(v-if="errors.has('email')") {{ errors.first('email') }}

        .field
          label.label(for="card") Credit Card
          p.help
            | กรุณาใช้เลข credit card:&nbsp;
            strong 4242 4242 4242 4242,<br>
            | และใส่รหัสไปรษณีย์ 5 หลัก

        .field
          card.stripe-card.input#card(:class="{ 'complete': isStripeCardCompleted }",
                                      :stripe="stripePublishableKey",
                                      @change="setIsStripeCardCompleted($event.complete)")

        .field
          button.button.is-success.pay-with-stripe(:disabled="!isStripeCardCompleted || errors.any()",
                                                   :class="{ 'is-loading': isLoading }")
            | ชำระด้วยบัตร

      .statusFailure.has-text-centered(v-if="status === 'failure'")
        h3 ไม่นะ!
        p มีบางอย่างผิดพลาด!
        button.button(@click="clearCheckout") ลองใหม่อีกครั้ง

</template>

<script>
import { Card } from 'vue-stripe-elements-plus'
import { createNamespacedHelpers } from 'vuex'

const { mapActions, mapGetters } = createNamespacedHelpers('checkout')
const STRIPE_URL = process.env.STRIPE_URL

export default {
  name: 'Checkout',
  components: {
    Card
  },
  computed: {
    ...mapGetters(['isStripeCardCompleted', 'status', 'isLoading'])
  },
  props: {
    total: {
      type: [Number, String],
      required: true
    },
    stripeUrl: {
      type: String,
      default: STRIPE_URL
    }
  },
  data() {
    return {
      userEmail: undefined,
      stripePublishableKey: process.env.STRIPE_PUBLISHABLE_KEY
    }
  },
  methods: {
    ...mapActions([
      'clearCheckout',
      'pay',
      'setIsStripeCardCompleted',
      'setStatus'
    ]),

    async beforePay() {
      const isAllFieldsValid = await this.$validator.validateAll()
      if (!isAllFieldsValid) {
        this.setStatus('failure')
        return
      }

      await this.pay({
        userEmail: this.userEmail,
        total: this.total,
        url: this.stripeUrl
      })
    }
  }
}
</script>

<style scoped lang="stylus">
.payment
  border 1px solid #ccc
  max-width 500px
  padding 50px
  display flex
  flex-direction column
  margin 0 auto

.stripe-card
  margin-bottom 10px

  &.input
    display block

/* -- transition --*/
.fade-enter-active,
.fade-leave-active
  transition opacity 0.25s ease-out

.fade-enter,
.fade-leave-to
  opacity 0
</style>
