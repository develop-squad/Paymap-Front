<template>
  <div id="map" class="map" @click="onClickMap">
  </div>
</template>

<script>
import axios from "axios"

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
    axios.get("/statics/geodata/REGIONS.json").then((response) => {
      this.regionGeoData = response.data
    })
    /* axios.get("/statics/geodata/TL_SCCO_SIG.json").then((response) => {
      this.regionGeoData = response.data
      const data = []
      for (const regionGeoPolygon of this.regionGeoData.features) {
        const code = regionGeoPolygon.properties.SIG_CD
        const coordinates = regionGeoPolygon.geometry.coordinates[0]
        const datum = {}
        datum.code = code
        datum.name = regionGeoPolygon.properties.SIG_KOR_NM
        datum.lng = 0.0
        datum.lat = 0.0
        datum.radius = 0.0
        for (const coordinate of coordinates) {
          datum.lng += coordinate[0] / coordinates.length
          datum.lat += coordinate[1] / coordinates.length
        }
        for (const coordinate of coordinates) {
          const distance = this.getWGSDistance(datum.lat, datum.lng, coordinate[1], coordinate[0])
          if (distance > datum.radius) {
            datum.radius = distance
          }
        }
        data.push(datum)
      }
      console.log(JSON.stringify(data))
    }) */
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
        position: position,
        icon: {
          content: [
            "<img class=\"image-marker\" src=\"/statics/images/marker.png\"/>",
            `<div class="label-marker">${name}</div>`
          ].join(""),
          size: new window.naver.maps.Size(20, 20)
        }
      })
      /* const infoWindow = new window.naver.maps.InfoWindow({
        content: `${name}`,
        minWidht: 100,
        maxWidth: 140,
        backgroundColor: "#eee",
        borderColor: "#ddd",
        borderWidth: 1,
        anchorSize: new window.naver.maps.Size(0, 0),
        anchorSkew: false,
        anchorColor: "#eee",
        pixelOffset: new window.naver.maps.Point(0, 0)
      })
      infoWindow.open(this.map, marker)
      window.naver.maps.Event.addListener(marker, "mouseover", e => {
        infoWindow.open(this.map, marker)
      })
      window.naver.maps.Event.addListener(marker, "mouseout", e => {
        infoWindow.close()
      }) */
      // this.map.setCenter(position)
      this.markers.push(marker)
    },
    onShopDataResponse (response) {
      const centerLng = this.map.getCenter().lng()
      const centerLat = this.map.getCenter().lat()
      const shopNames = []
      let shopAddresses = ""
      if (response.data.length > 0) {
        for (const shop of response.data) {
          shopNames.push(shop.shop_name)
          shopAddresses += shop.shop_address + "|"
        }
        shopAddresses = shopAddresses.substr(0, shopAddresses.length - 1)
        // 가맹점 좌표 데이터 요청
        axios.get("http://devx.kr:9991/geocode", {
          params: {
            address: shopAddresses,
            coordinate: centerLng + "," + centerLat
          }
        }).then((response) => {
          let i = 0
          for (const datum of response.data) {
            const addresses = datum.addresses
            if (typeof addresses !== "undefined" && addresses.length > 0) {
              // 마커 활성화
              this.showMarker(shopNames[i++], addresses[0].y, addresses[0].x)
            }
          }
        })
      }
    },
    showMarkersByQuery (query, type) {
      this.removeMarkers()
      const regionQueue = this.getCurrentRegions()
      // 지역 내 가맹점 데이터 요청
      for (const region of regionQueue) {
        axios.get("http://devx.kr:9991/zeropay", {
          params: {
            sido: region.SIDO_CD,
            sigungu: region.SIGUNGU_CD,
            name: query,
            type: type
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
      const centerLng = this.map.getCenter().lng()
      const centerLat = this.map.getCenter().lat()
      const boundsLng = this.map.getBounds()._max._lng
      const boundsLat = this.map.getBounds()._max._lat
      const screenRadius = this.getWGSDistance(centerLat, centerLng, boundsLat, boundsLng)
      const regionQueue = []
      for (const region of this.regionGeoData) {
        if (this.getWGSDistance(centerLat, centerLng, region.lat, region.lng) <= region.radius + screenRadius) {
          console.log(region.name)
          regionQueue.push(this.regionData[region.code])
        }
      }
      return regionQueue
    },
    getWGSDistance (lat1, lng1, lat2, lng2) {
      function deg2rad (deg) {
        return deg * Math.PI / 180
      }
      var R = 6371
      var dLat = deg2rad(lat2 - lat1)
      var dLon = deg2rad(lng2 - lng1)
      var a = Math.sin(dLat / 2) * Math.sin(dLat / 2) + Math.cos(deg2rad(lat1)) * Math.cos(deg2rad(lat2)) * Math.sin(dLon / 2) * Math.sin(dLon / 2)
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a))
      var d = R * c
      return d
    }
  }
}
</script>
