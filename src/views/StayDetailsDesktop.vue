<template>
  <DetailsHeader v-if="showDetailsHeader" @submitOrder="submitOrder" />

  <div v-if="stay">
    <main class="stay-details">
      <StayHeaderInfo :stay="stay" />
      <StayDetailsImgs :stay="stay" />

      <!-- Details-container -->
      <section class="details-container">
        <!-- ferrites -->
        <div class="stay-ferrites">
          <div class="ferrites-header">
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
                  Superhosts are experienced, highly rated hosts who are committed to providing
                  great stays for guests.
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
            <!-- <img src="../../imgs_test/aircover_logo.webp" alt="" srcset="" /> -->
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
              <div v-for="amenitie in this.stay.amenities" class="offer">
                <img :src="'../src/imgs/amenities/' + amenitie + '.svg'" />
                <p>{{ amenitie }}</p>
              </div>
              <p
                v-if="displayedAmenities.length < stay.amenities.length"
                @click="showMoreAmenities">
                Show more
              </p>
            </div>
          </section>
        </div>

        <DetailsOrderBox :searchDetails="searchDetails" :stay="stay" />
      </section>
    </main>

    <DetailsReviews :stay="stay" />
    <section class="map">
      <h3>Where you'll be</h3>
      <GoogleMap :stay="stay" />
      <div class="place-details-txt">
        <h3>{{ stay.loc.city }}, {{ stay.loc.country }}</h3>
        <p>{{ stay.summary }}</p>
      </div>
    </section>

    <section class="host-info">
      <div class="header">
        <img :src="stay.host.thumbnailUrl" />
        <h3>Hosted by {{ stay.host.fullname }}</h3>
      </div>
      <section class="flex">
        <section class="left-side">
          <div class="info">
            <span
              ><img src="../imgs/svg_symbols/black_star.svg" />
              {{ stay.reviews.length }} reviews</span
            >
            <span><img src="../imgs/svg_symbols/verified.svg" /> Identity verified </span>
            <span><img src="../imgs/svg_symbols/superhost.png" /></span>
            <span v-if="stay.host.isSuperHost">Superhost </span>
          </div>
          <div class="txt">
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
        </section>

        <section class="right-side">
          <p>Languages: English, Türkçe</p>
          <p>Response rate: 100%</p>
          <p>Response time: within an hour</p>
          <div class="protect flex">
            <img src="../imgs/svg_symbols/protect_logo.svg" />
            <p>
              To protect your payment, never transfer money or communicate outside of the Airgpt
              website or app
            </p>
          </div>
        </section>
      </section>
    </section>
  </div>
</template>

<script>
import DetailsOrderBox from '../cmps/DetailsOrderBox.vue'
import DetailsHeader from '../cmps/DetailsHeader.vue'
import StayHeaderInfo from '../cmps/StayHeaderInfo.vue'
import StayDetailsImgs from '../cmps/StayDetailsImgs.vue'
import DetailsReviews from '../cmps/DetailsReviews.vue'
import GoogleMap from '../cmps/GoogleMap.vue'

export default {
  name: 'StayDetailsDesktop',

  data() {
    return {
      showDetailsHeader: false,
      stay: null,
      searchDetails: null,
      scrollPositionToShowDetailsHeader: 750,
      displayedAmenities: [],
      maxAmenities: 6,
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
    console.log('this.stay', this.stay)
    const { where } = this.$route.query
    if (!where) {
      this.searchDetails.where = this.stay.loc.country

      this.displayedAmenities = this.stay.amenities.slice(0, this.maxAmenities)
    }
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll)

    const currRoute = this.$route.path
    if (currRoute.includes('stay')) {
      document.getElementById('main-app').classList.value = 'main-container details-grid'
    }
    this.searchDetails = this.$route.query
    const { adults, children, infants, pets } = this.$route.query

    this.searchDetails.guestNum = +adults + +children + +infants + +pets
  },

  destroyed() {
    window.removeEventListener('scroll', this.handleScroll)
  },

  methods: {
    showMoreAmenities() {
      this.maxAmenities += 6
      this.displayedAmenities = this.stay.amenities.slice(0, this.maxAmenities)
    },

    getAmemitie(amenitie) {
      console.log('amenitie:', amenitie)
      return `../../src/imgs/amenities/${amenitie.toLowerCase()}.svg/>`
    },
    async loadStay() {
      const { stayId } = this.$route.params

      this.$store.dispatch({ type: 'getStay', stayId: stayId })
    },

    handleScroll() {
      const scrollPosition = window.scrollY
      if (scrollPosition > this.scrollPositionToShowDetailsHeader) {
        this.showDetailsHeader = true
      } else {
        this.showDetailsHeader = false
      }
    },
  },
  components: {
    DetailsOrderBox,
    StayDetailsImgs,
    DetailsReviews,
    StayHeaderInfo,
    GoogleMap,
    DetailsHeader,
  },
}
</script>
