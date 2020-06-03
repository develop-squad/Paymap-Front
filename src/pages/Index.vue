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

<style lang="scss" scoped>
.map {
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 0;
}
</style>
