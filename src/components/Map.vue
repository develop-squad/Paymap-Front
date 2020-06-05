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
    zoom: Number
  },
  data () {
    return {
      /**
       * @type {naver.maps.Map}
       */
      map: null
    }
  },
  mounted () {
    this.setCenterToGeolocation()
    this.drawMap()
    window.naver.maps.Event.once(this.map, "init_stylemap", this.mapInit)
  },

  methods: {
    onClickMap () {
      this.$parent.$parent.$parent.topCardOpened = !this.$parent.$parent.$parent.topCardOpened
    },
    drawMap () {
      const options = {
        center: new window.naver.maps.LatLng(this.lat, this.lng),
        zoom: this.zoom,
        zoomControl: true,
        zoomControlOptions: {
          style: window.naver.maps.ZoomControlStyle.SMALL,
          position: window.naver.maps.Position.RIGHT_BOTTOM
        }
      }
      this.map = new window.naver.maps.Map("map", options)
    },
    mapInit () {
      const locationButtonHtml = "<a href=\"#\" class=\"btn-location\"><span class=\"ico-location\">접속위치</span></a>"
      const locationButton = new window.naver.maps.CustomControl(locationButtonHtml, {
        position: window.naver.maps.Position.RIGHT_BOTTOM
      })
      locationButton.setMap(this.map)
      window.naver.maps.Event.addDOMListener(locationButton.getElement(), "click", this.setCenterToGeolocation)

      locationButton.getElement().parentNode.parentNode.className = "control-wrapper"
    },
    updatePosition (position) {
      const center = new window.naver.maps.LatLng(position.coords.latitude, position.coords.longitude)
      this.map.setCenter(center)
    },
    setCenterToGeolocation () {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.updatePosition)
      }
    }
  }
}
</script>
