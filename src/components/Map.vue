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
    },
    showMarker (name, lat, lng) {
      const position = new window.naver.maps.LatLng(lat, lng)
      const marker = new window.naver.maps.Marker({
        map: this.map,
        position: position
      })
      const infoWindow = new window.naver.maps.InfoWindow({
        content: `<h4>${name}</h4>`,
        minWidht: 100,
        maxWidth: 140,
        backgroundColor: "#eee",
        borderColor: "#2db400",
        borderWidth: 3,
        anchorSize: new window.naver.maps.Size(30, 30),
        anchorSkew: true,
        anchorColor: "#eee",
        pixelOffset: new window.naver.maps.Point(20, -20)
      })
      window.naver.maps.Event.addListener(marker, "click", e => {
        if (infoWindow.getMap()) {
          infoWindow.close()
        } else {
          infoWindow.open(this.map, marker)
        }
      })
      this.map.setCenter(position)
      console.log("marker created")
    }
  }
}
</script>
