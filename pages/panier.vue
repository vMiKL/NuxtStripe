<template lang="pug">
  client-only
    v-container
      //- v-row(justify="center")
        v-col(cols="12" md="12").text-center
      v-row(justify="center").mt-16
        v-col(cols="12" md="6")
          v-card(outlined)
            v-toolbar(flat color="#f7f7f7")
              h2 Stripe Elements - Card
              v-spacer
              a(href="https://vuestripe.com/stripe-elements/card"  target="_blank") See Docs
            v-card-text.pt-10
              stripe-element-card(
                ref="cardRef"
                :pk="pk"
                hide-postal-code
                @token="tokenCreated"
                @loading="v => loading = v"
              )
              pre {{token}}
            v-card-actions
              v-spacer
              v-btn(
                depressed
                large
                color="primary"
                :loading="loading"
                :disabled="loading"
                @click="$refs.cardRef.submit()"
              ).text-none Generate token
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
    checkout () {
      this.loading = true;
      this.$refs.checkoutRef.redirectToCheckout();
    },
    tokenCreated (token) {
      this.token = token;
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
