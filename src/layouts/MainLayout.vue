<template>
  <q-layout view="lHh Lpr lFf" class="layout">
    <q-slide-transition>
      <div class="top-card" v-if="topCardOpened">
        <q-form class="top-card__input" @submit="onSearch">
          <q-input color="grey-3" filled v-model="search" />
          <q-btn  label="검색" @click="onSearch"/>
        </q-form>
        <div class="top-card__btn">
          <q-btn-dropdown :label="type">
            <q-list>
              <q-item clickable v-close-popup @click="onClickType(type)" v-for="(type, index) in typeList" :key="index">
                <q-item-section>
                  <q-item-label>{{type}}</q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-btn-dropdown>
        </div>
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
      iPhoneIndicatorCheck: null,
      typeList: [
        "전체",
        "건어물",
        "마트",
        "문구",
        "미용",
        "병원",
        "부동산",
        "분식",
        "생화",
        "생활용품",
        "수산물",
        "슈퍼",
        "식육",
        "안경",
        "약국",
        "양식",
        "어린이집",
        "유통",
        "음식",
        "의류",
        "일식",
        "잡화",
        "전자상거래",
        "정비",
        "제과",
        "주류",
        "주유",
        "중식",
        "치과",
        "치킨",
        "카페",
        "커피",
        "편의점",
        "피자",
        "학원",
        "한식",
        "한의원",
        "호프",
        "화장품",
        "휴게음식"
      ],
      type: "카테고리"
    }
  },
  mounted () {
    this.iPhoneXCheck()
  },
  methods: {
    onSearch () {
      console.log("Search", this.search)
      // api 호출
    },
    onClickType (type) {
      this.type = type
      console.log(type)
      // api 호출
      this.$refs.index.$refs.map.showMarkersByType(type)
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

<style lang="scss">
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
        .q-field__control {
          height: 40px;
        }
      }

      button {
        width: 20%;
      }
    }

    &__btn{
      padding: 0 10px 10px;
      button {
        width: 100%;
      }
    }
  }
}
</style>
