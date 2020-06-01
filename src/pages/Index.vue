<template>
  <q-page class="flex flex-center">
    <Map
      v-bind:lat="126.973622"
      v-bind:lng="37.563219"
      v-bind:zoom="17"
    />
  </q-page>
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
  background-color: greenyellow;
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: -1;
}
</style>
