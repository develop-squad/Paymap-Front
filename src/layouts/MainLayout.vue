<template>
  <q-layout view="lHh Lpr lFf" class="layout">
    <div class="top-card">
      상단바
    </div>

    <div class="content">
      <router-view />
    </div>

    <div class="bottom-card">
      하단바
    </div>
  </q-layout>
</template>

<script>
export default {
  name: "MainLayout"
}
</script>

<style lang="scss" scoped>
.layout {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  .top-card {
    z-index: 1;
    background-color: white;
    margin: 10px;
    padding: 10px;
  }

  .content {
    position: absolute;
    top: 0;
    left: 0;
  }

  .bottom-card {
    z-index: 1;
    background-color: white;
    margin: 10px;
    padding: 10px;
  }
}
</style>
