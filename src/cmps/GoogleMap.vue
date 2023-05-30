<template>
  <GoogleMap
    api-key="AIzaSyC72v9jkjDUUxDsv3CPtG2Vu_MThEk9JKs"
    style="width: 100%; height: 500px"
    :center="pos1"
    :zoom="14">
    <Marker @click="sayHi" :options="{ position: pos1, label: 'Hey', title: 'LADY LIBERTY' }" />
    <Marker :options="{ position: pos1 }" />
    <!-- <Marker :options="{ position: pos3 }" /> -->
  </GoogleMap>
</template>

<script>
import { GoogleMap, Marker } from 'vue3-google-map'

export default {
  components: { GoogleMap, Marker },
  props: {
    stay: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {
      pos1: { lat: this.stay.loc.lan, lng: this.stay.loc.lat },
    }
  },
}
</script>
