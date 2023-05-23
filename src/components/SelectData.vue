<template>
  <!-- 遮罩层 -->
  <div class="overlay" v-show="isShow"></div>
  <div class="search-container">
    <form>
      <input type="text" class="search-input" placeholder="输入BV号搜索..." v-model="BVNumber">
      <input type="button" @click="getSearchData" value="搜索" class="search-button">
      <button type="button" class="more-button" @click="togglePopup">更多筛选</button>
    </form>
  </div>
</template>

<script>
import searchBus from "../assets/js/DataBus.js";
import axios from "axios";

export default {
  data() {
    return {
      BVNumber: "",
      isShow: false,
      checkbox: [],
      sortValue: "",
      selectField: "username",
      time: null,
      fuzzyQueryText: "",
      fieldSearchText: "",
    }
  },
  mounted() {
    searchBus.on("updateShow", (isShowPop) => {
      this.isShow = isShowPop;
    });
    searchBus.on("updateCheckbox", (checkbox) => {
      this.checkbox = checkbox;
    });
    searchBus.on("updateSortValue", (sortValue) => {
      this.sortValue = sortValue;
    });
    searchBus.on("updateSelectField", (selectField) => {
      this.selectField = selectField;
    });
    searchBus.on("updateTime", (time) => {
      this.time = time;
    });
    searchBus.on("updateFuzzyQueryText", (fuzzyQueryText) => {
      this.fuzzyQueryText = fuzzyQueryText;
    });
    searchBus.on("updateFieldSearchText", (fieldSearchText) => {
      this.fieldSearchText = fieldSearchText;
    });
  },
  methods: {
    togglePopup() {
      this.isShow = true;
      searchBus.emit('updateShowPop', this.isShow);
    },
    getSearchData() {
      const request = {
        "pageNum": 1,
        "pageSize": 50,
        "indexName": this.BVNumber,
        "type": "1",
        ...(this.fuzzyQueryText !== '' && { queryString: this.fuzzyQueryText })
      };
      axios.post(`/api/biliComment/getDocument`, request)
          .then(response => {
            // 请求成功的处理逻辑
            console.log(response.data);
          })
          .catch(error => {
            // 请求失败的处理逻辑
            console.error(error);
          });
    }
  }
}
</script>