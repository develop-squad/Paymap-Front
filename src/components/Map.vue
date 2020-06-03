<template>
  <div id="map" class="map">
  </div>
</template>

<script>
export default {
  name: "Map",
  props: {
    lat: Number,
    lng: Number,
    zoom: Number,
    mapOptions: Object
  },
  data () {
    return {
      /**
       * @type {naver.maps.Map}
       */
      map: null
    }
  },
  watch: {
    mapOptions: {
      handler (newValue) {
        this.map.setOptions(newValue)
      }
    }
  },

  mounted () {
    const options = {
      center: new window.naver.maps.LatLng(this.lat, this.lng),
      zoom: this.zoom,
      zoomControl: true,
      zoomControlOptions: {
        style: window.naver.maps.ZoomControlStyle.LARGE,
        position: window.naver.maps.Position.RIGHT_CENTER
      }
    }
    // let map = new naver.maps.Map('map', initOptions)
    this.map = new window.naver.maps.Map("map", options)
  }
}
</script>
