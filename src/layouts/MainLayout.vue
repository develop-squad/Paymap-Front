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
      <router-view ref="index" />
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
      topCardOpened: true,
      iPhoneIndicatorCheck: null
    }
  },
  mounted () {
    this.iPhoneXCheck()
  },
  methods: {
    onSearch () {
      console.log("Search", this.search)
    },
    iPhoneXCheck () {
      if (navigator.userAgent.includes("iPhone")) {
        var ratio = screen.height / screen.width
        if (ratio > 2) {
          // 홈 인디케이터 관련 스타일 추가
          this.iPhoneIndicatorCheck = true
        } else {
          this.iPhoneIndicatorCheck = false
        }
      }
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
    //border-radius: 30px;
    background: linear-gradient( to right, #64fd31, #2Db400 );
    color: white;
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
