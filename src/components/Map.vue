<template>
  <div id="map" class="map" @click="onClickMap">
  </div>
</template>

<script>
import axios from "axios"
import intersect from "@turf/intersect"

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
      map: null,
      markers: [],
      regionData: {},
      regionGeoData: {
        features: {}
      }
    }
  },
  mounted () {
    axios.get("/statics/geodata/LAWDCD.json").then((response) => {
      for (const datum of response.data) {
        this.regionData[datum.LAWD_CD] = datum
      }
    })
    axios.get("/statics/geodata/TL_SCCO_SIG.json").then((response) => {
      this.regionGeoData = response.data
    })
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
      // this.map.setCenter(position)
      this.markers.push(marker)
    },
    onShopDataResponse (response) {
      const centerLng = this.map.getCenter().lng()
      const centerLat = this.map.getCenter().lat()
      for (const shop of response.data) {
        // 가맹점 좌표 데이터 요청
        axios.get("http://devx.kr:9991/geocode", {
          params: {
            address: shop.shop_address,
            coordinate: centerLng + "," + centerLat
          }
        }).then((response) => {
          const addresses = response.data.addresses
          if (typeof addresses !== "undefined" && addresses.length > 0) {
            // 마커 활성화
            this.showMarker(shop.shop_name, addresses[0].y, addresses[0].x)
          }
        })
      }
    },
    showMarkersByQuery (query) {
      this.removeMarkers()
      const regionQueue = this.getCurrentRegions()
      // 지역 내 가맹점 데이터 요청
      for (const region of regionQueue) {
        axios.get("http://devx.kr:9991/zeropay", {
          params: {
            sido: region.SIDO_CD,
            sigungu: region.SIGUNGU_CD,
            name: query
          }
        }).then(this.onShopDataResponse)
      }
    },
    showMarkersByType (type) {
      this.removeMarkers()
      const regionQueue = this.getCurrentRegions()
      // 지역 내 가맹점 데이터 요청
      for (const region of regionQueue) {
        axios.get("http://devx.kr:9991/zeropay", {
          params: {
            sido: region.SIDO_CD,
            sigungu: region.SIGUNGU_CD,
            type: type
          }
        }).then(this.onShopDataResponse)
      }
    },
    removeMarkers () {
      for (const marker of this.markers) {
        marker.setMap(null)
      }
      this.markers = []
    },
    getCurrentRegions () {
      // 현재 지도에 보이는 영역
      const bounds = this.map.getBounds()
      const boundsGeoPolygon = {
        type: "Feature",
        properties: {},
        geometry: {
          type: "Polygon",
          coordinates: [[
            [bounds._min.x, bounds._min.y],
            [bounds._max.x, bounds._min.y],
            [bounds._max.x, bounds._max.y],
            [bounds._min.x, bounds._max.y]
          ]]
        }
      }
      // 현재 지도에 보이는 지역(시/군/구)
      const regionQueue = []
      let intersection
      for (const regionGeoPolygon of this.regionGeoData.features) {
        intersection = intersect(boundsGeoPolygon, regionGeoPolygon)
        if (intersection !== null) {
          console.log(regionGeoPolygon.properties.SIG_KOR_NM)
          regionQueue.push(this.regionData[regionGeoPolygon.properties.SIG_CD])
        }
      }
      return regionQueue
    }
  }
}
</script>
