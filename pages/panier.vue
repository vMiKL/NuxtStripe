<template lang="pug">
v-container
  v-row.mt-16(justify="center")
    v-col(cols="12", md="6")
      v-card(outlined)
        v-card-text.text-center
          img(width="200", src="../assets/common-sense.jpeg")
          h2 Buy common sense for $10.000
          stripe-checkout(
            ref="checkoutRef",
            mode="payment",
            :pk="pk",
            :line-items="lineItems",
            :success-url="successURL",
            :cancel-url="cancelURL",
            @loading="(v) => (loading = v)"
          )
          br
          v-alert(
            dark,
            v-if="redirectState",
            :color="redirectState",
            dismissible
          ) Payment {{ redirectState }}
        v-card-actions
          v-spacer
          v-btn.text-none(
            depressed,
            large,
            color="primary",
            :loading="loading",
            :disabled="loading",
            @click="checkout"
          ) Pay $10
</template>

<script>
import headMeta from '~/utils/head-meta';

export default {
  data () {
    this.pk = 'pk_test_KRUvZiEQgpnKHiwV9rc88RjE';
    return {
      loading: false,
      lineItems: [
        {
          price: 'price_1KBKLLLdPoxuSz0o2bEbH3kT', // L'identifiant du prix unique que vous avez créé dans votre tableau de bord Stripe, création d'un article
          quantity: 1,
        },
      ],
      token: null,
      successURL: process.client && `${window.location.origin}${window.location.pathname}?state=success`,
      cancelURL: process.client && `${window.location.origin}${window.location.pathname}?state=error`,
      // Misc
      redirectState: '',
    };
  },
  computed: {
    queryState () {
      return process.client && (new URLSearchParams(window.location.search).get('state') || '');
    },
  },
  mounted () {
    if (this.queryState === 'success') {
      this.redirectState = 'success';
    }
    if (this.queryState === 'error') {
      this.redirectState = 'error';
    }
  },
  methods: {
    async checkout () {
      this.loading = true;
      const stripe = require('stripe')(this.pk);
      await stripe.paymentIntents.create({ amount: 2000, currency: 'usd', payment_method_types: ['card'] });
    },
  },
  head () {
    return headMeta({
      title: 'Vue Stripe + Nuxt.js Demo',
      description: 'A demo on how to implement Vue Stripe in Nuxt.js',
      socialBanner: require('../assets/vue-stripe-logo-variant-1-small.png'),
    });
  },
};
</script>
