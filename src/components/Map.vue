<template>
  <div id="map" class="map" @click="onClickMap">
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
      zoom: this.zoom
    }
    // let map = new naver.maps.Map('map', initOptions)
    this.map = new window.naver.maps.Map("map", options)
  },
  methods: {
    onClickMap () {
      this.$parent.$parent.$parent.topCardOpened = !this.$parent.$parent.$parent.topCardOpened
    }
  }
}
</script>

<style>
.map {
  width: 100vw;
  height: 100vh;
  min-height: inherit;
}
</style>
