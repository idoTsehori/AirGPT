<template>
  <section class="stay-review-mobile">
    <h1>{{ stay.name }}</h1>
    <div class="info flex" style="gap: 0.8em">
      <img src="../imgs/svg_symbols/black_star.svg" />
      <span class="bold-font">{{ stay.rate }}</span>
      <span>•</span>
      <span class="underline bold-font">{{ reviewsNum }} </span>
      <span>•</span>
      <span><img src="../imgs/svg_symbols/superhost.png" /></span>
      <span v-if="stay.host.isSuperHost">Superhost •</span>
      <span class="bold-font underline">{{ stay.loc.city }}, {{ stay.loc.country }}</span>
    </div>
  </section>
</template>

<script>
export default {
  name: 'StayHeaderInfoMobile',
  props: {
    stay: {
      type: Object,
      required: true,
    },
  },

  computed: {
    reviewsNum() {
      if (this.stay.reviews.length === 1) return '1 review'
      else if (!this.stay.reviews.length) return 'no reviews'
      else return this.stay.reviews.length + ' reviews'
    },
  },
}
</script>
