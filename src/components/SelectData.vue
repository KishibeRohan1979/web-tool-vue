<template>
  <!-- 遮罩层 -->
  <div class="overlay" v-show="isShow"></div>
  <div class="search-container">
    <form>
      <input type="text" class="search-input" placeholder="输入BV号搜索..." v-model="BVNumber">
      <input type="button" value="搜索" class="search-button">
      <button type="button" class="more-button" @click="togglePopup">更多筛选</button>
    </form>
  </div>
</template>

<script>
import searchBus from "../assets/js/DataBus.js";

export default {
  data() {
    return {
      BVNumber: "",
      isShow: false
    }
  },
  mounted() {
    searchBus.on("updateShow", (isShowPop) => {
      this.isShow = isShowPop;
    });
  },
  methods: {
    togglePopup() {
      this.isShow = true;
      searchBus.emit('updateShowPop', this.isShow);
    }
  }
}
</script>