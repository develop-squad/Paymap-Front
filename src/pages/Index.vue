<template>
  <div>
    <Map
      v-bind:lat="126.973622"
      v-bind:lng="37.563219"
      v-bind:zoom="17"
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
