<template>
  <div>
    <Map
      v-bind:lat="37.563219"
      v-bind:lng="126.973622"
      v-bind:zoom="16"
    />
  </div>
</template>

<script>
import Map from "components/Map"

export default {
  name: "PageIndex",
  components: {
    Map
  }
}
</script>

<style lang="scss">
.map {
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 0;
  .btn-location {
    overflow: hidden;
    display: inline-block;
    position: absolute;
    top: -30px;
    left: 6px;
    width: 34px;
    height: 34px;
    border: 1px solid rgba(58,70,88,.45);
    border-radius: 2px;
    background: #fcfcfd;
    text-align: center;
    -webkit-background-clip: padding;
    background-clip: padding-box;
    .ico-location {
      width: 20px;
      height: 20px;
      margin: 7px 0 0 0;
      background-position: -153px -31px;
      overflow: hidden;
      display: inline-block;
      color: transparent!important;
      vertical-align: top;
      background-image: url(https://ssl.pstatic.net/static/maps/m/spr_trff_v6.png);
      background-size: 200px 200px;
      -webkit-background-size: 200px 200px;
    }
  }
}
</style>
