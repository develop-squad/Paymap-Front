<template>
  <q-layout view="lHh Lpr lFf" class="layout">
    <q-slide-transition>
      <div class="top-card" v-if="topCardOpened">
        <q-form class="top-card__input" @submit="onSearch">
            <q-input filled v-model="search" />
            <q-btn  label="검색" @click="onSearch"/>
          </q-form>
        </div>
      </q-slide-transition>

    <div class="content">
      <router-view />
    </div>
<!--
    <div class="bottom-card">
      하단바
    </div>
  -->
  </q-layout>
</template>

<script>
export default {
  name: "MainLayout",
  data () {
    return {
      search: "",
      topCardOpened: true
    }
  },
  methods: {
    onSearch () {
      console.log("Search", this.search)
    }
  }
}
</script>

<style lang="scss" scoped>
.layout {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  .top-card, .bottom-card  {
    z-index: 1;
    margin: 10px;
  }

  .content {
    position: absolute;
    top: 0;
    left: 0;
    height: 100vh;
    width: 100vw
  }

  .top-card {
    &__input{
      padding: 10px;
      display: flex;
      justify-content: space-between;
      label {
        width: 78%;
      }

      button {
        width: 20%;
      }
    }
  }
}
</style>
