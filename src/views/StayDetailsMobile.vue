<template>
  <StayImgPreview class="imgs-details-carusell full" :imgs="stay.imgUrls" />
  <main v-if="stay" class="stay-details-mobile">
    <StayHeaderInfoMobile :stay="stay" />
    <!-- Details-container -->
    <section class="details-container-mobile">
      <!-- ferrites -->
      <div class="stay-ferrites-mobile">
        <div class="ferrites-header-mobile">
          <div class="header-text">
            <h3>Entire amazing views hosted by {{ stay.host.fullname }}</h3>
            <p>{{ guestsNum }} • {{ bedroomNum }} • {{ bedsNum }} • {{ bathsNum }}</p>
          </div>
          <img :src="stay.host.thumbnailUrl" />
        </div>

        <div class="ferrites-main">
          <div v-if="stay.host.isSuperHost" class="line">
            <img src="../../src/imgs/ferrites_imgs/superhost.svg" />
            <div class="text">
              <h4>{{ stay.host.fullname }} is a Superhost</h4>
              <p>
                Superhosts are experienced, highly rated hosts who are committed to providing great
                stays for guests.
              </p>
            </div>
          </div>
          <div class="line">
            <img src="../../src/imgs/ferrites_imgs/checkin.svg" />
            <div class="text">
              <h4>Great check-in experience</h4>
              <p>100% of recent guests gave the check-in process a 5-star rating.</p>
            </div>
          </div>

          <div class="line">
            <img src="../../src/imgs/ferrites_imgs/location.svg" />
            <div class="text">
              <h4>Free cancellation for 48 hours.</h4>
            </div>
          </div>
        </div>

        <!-- aircover -->
        <section class="aircover">
          <img src="../../src/imgs/aircover_logo/aircover_logo.webp" alt="aircover" />
          <p>
            Every booking includes free protection from Host cancellations, listing inaccuracies,
            and other issues like trouble checking in.
          </p>
          <a href="">Learn more</a>
        </section>

        <!-- ameneties -->
        <section class="ameneties">
          <h3>What this place offers</h3>
          <div class="offers grid">
            <div v-for="amenitie in stay.amenities" class="offer">
              <img :src="'../../src/imgs/amenities/' + amenitie + '.svg'" />
              <p>{{ amenitie }}</p>
            </div>
          </div>
        </section>
      </div>

      <!-- <DetailsOrderBox :searchDetails="searchDetails" :stay="stay" /> -->
    </section>
  </main>

  <!-- <DetailsReviewsMobile :stay="stay" /> -->

  <section style="margin: 0" class="map">
    <h3 style="margin: 15px">Where you'll be</h3>
    <GoogleMap style="width: 85%; height: 85%; margin: auto" :stay="stay" />

    <div class="place-details-txt">
      <h3>{{ stay.loc.city }}, {{ stay.loc.country }}</h3>
      <p>{{ stay.summary }}</p>
    </div>
  </section>

  <!-- <section style="margin-top: 15em" class="host-info-mobile">
    <div class="header-mobile">
      <h3>Hosted by {{ stay.host.fullname }}</h3>
      <img :src="stay.host.imgUrl" />
    </div>
    <section class="left-side-mobile">
      <div class="info-mobile">
        <span
          ><img src="../imgs/svg_symbols/black_star.svg" /> {{ stay.reviews.length }} reviews</span
        >
        <span
          ><img src="../imgs/svg_symbols/verified.svg" alt="" srcset="" /> Identity verified
        </span>
        <span v-if="stay.host.isSuperHost"
          ><img src="../imgs/svg_symbols/superhost.png" />Superhost</span
        >
      </div>
      <div class="txt-mobile">
        {{ stay.host.description }}
        <h5>During your stay</h5>
        <p>They can reach me anytime they want. Contact number 0536 555 55 55</p>
        <div v-if="stay.host.isSuperHost">
          <h5>{{ stay.host.fullname }} is a Superhost</h5>
          <p>
            Superhosts are experienced, highly rated hosts who are committed to providing great
            stays for guests
          </p>
        </div>
      </div>
      <p>Languages: English, Türkçe</p>
      <p>Response rate: 100%</p>
      <p>Response time: within an hour</p>
      <div class="protect-mobile flex">
        <img src="../imgs/svg_symbols/protect_logo.svg" alt="" srcset="" />
        <p>
          To protect your payment, never transfer money or communicate outside of the Airgpt website
          or app
        </p>
      </div>
    </section>
  </section> -->

  <StayDetailsFooterMobile />
</template>

<script>
import DetailsOrderBox from '../cmps/DetailsOrderBox.vue'
import StayHeaderInfoMobile from '../cmps/StayHeaderInfoMobile.vue'
import StayDetailsFooterMobile from '../cmps/StayDetailsFooterMobile.vue'

import StayDetailsImgs from '../cmps/StayDetailsImgs.vue'
import DetailsReviewsMobile from '../cmps/DetailsReviewsMobile.vue'
import GoogleMap from '../cmps/GoogleMap.vue'
import StayImgPreview from '../cmps/StayImgPreview.vue'

export default {
  name: 'StayDetailsMobile',
  data() {
    return {
      stay: null,
      searchDetails: null,
    }
  },

  computed: {
    guestsNum() {
      if (this.stay.capacity === 1) return '1 guest'
      return this.stay.capacity + ' guests'
    },
    bedsNum() {
      if (this.stay.equipment.bedsNum === 1) return '1 bed'
      return this.stay.equipment.bedsNum + ' beds'
    },
    bedroomNum() {
      if (this.stay.equipment.bedroomNum === 1) return '1 bedroom'
      return this.stay.equipment.bedroomNum + ' bedrooms'
    },

    bathsNum() {
      if (this.stay.equipment.bathNum === 1) return '1 bath'
      return this.stay.equipment.bathNum + ' baths'
    },
  },
  async created() {
    const { stayId } = this.$route.params
    const stay = await this.$store.dispatch({ type: 'getStay', stayId })
    this.stay = stay
    const { where } = this.$route.query
    if (!where) {
      this.searchDetails.where = this.stay.loc.country
    }
  },

  mounted() {
    const currRoute = this.$route.path
    if (currRoute.includes('stay')) {
      document.getElementById('main-app').classList.value = 'main-container details-grid-mobile'
    }
    this.searchDetails = this.$route.query
    const { adults, children, infants, pets } = this.$route.query

    this.searchDetails.guestNum = +adults + +children + +infants + +pets
  },

  methods: {
    async loadStay() {
      const { stayId } = this.$route.params
      this.$store.dispatch({ type: 'getStay', stayId: stayId })
    },
  },

  watch: {
    stay() {
      console.log('stay', this.stay)
    },
  },
  components: {
    DetailsOrderBox,
    StayDetailsImgs,
    DetailsReviewsMobile,
    StayHeaderInfoMobile,
    GoogleMap,
    StayImgPreview,
  },
}
</script>
