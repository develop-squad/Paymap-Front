<template>
  <div class="map" id="map">
    지도
  </div>
</template>

<script>
export default {
  name: "PageIndex",
  data () {
    return {
    }
  },
  methods: {
  }
}
</script>

<style lang="scss" scoped>
.map {
  background-color: greenyellow;
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: -1;
}
</style>
