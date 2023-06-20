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
      selectField: "uname",
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
      console.log(this.checkbox);
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
    getStartTime() {
      return Math.floor(new Date(this.time[0]).getTime() / 1000);
    },
    getEndTime() {
      return Math.floor(new Date(this.time[1]).getTime() / 1000);
    },
    togglePopup() {
      this.isShow = true;
      searchBus.emit('updateShowPop', this.isShow);
    },
    getSearchData() {
      console.log(this.checkbox);
      let termMap = {};
      let matchMap = {};
      if (this.checkbox.includes("badgeFans")) {
        termMap.isUpperFansCard = true;
        console.log(termMap);
      }
      if (this.checkbox.includes("fansOnly")) {
        termMap.isContractor = true;
      }
      if (this.checkbox.includes("fieldSearch")) {
        matchMap[this.selectField] = this.fieldSearchText;
      }
      const checkboxHasSearchTime = this.checkbox.includes("searchTime");
      const checkboxHasSearchTimeAnd3 = checkboxHasSearchTime && this.sortValue === "3";
      const request = {
        "pageNum": 1,
        "pageSize": 50,
        "orderField": checkboxHasSearchTime &&  this.sortValue !== "3"? "ctime" : "like",
        "orderType": checkboxHasSearchTime && this.sortValue === "2" ? "asc" : "desc",
        "indexName": this.BVNumber,
        "type": "1",
        termMap,
        matchMap,
        ...(checkboxHasSearchTimeAnd3 && {
          rangeField: "ctime",
          startValue: this.getStartTime(),
          endValue: this.getEndTime(),
        }),
        ...((this.checkbox.includes("fuzzyQuery") && this.fuzzyQueryText !== '') && { queryString: this.fuzzyQueryText }),
      };
      axios.post(`/api/biliComment/getDocument`, request)
          .then(response => {
            // 请求成功的处理逻辑
            searchBus.emit('updateCommentData', response.data);
          })
          .catch(error => {
            // 请求失败的处理逻辑
            console.error(error);
          });
      const topRequest = {
        "indexName": this.BVNumber,
        "pageNum": 1,
        "pageSize": 1,
        "type": "1",
        "orderField": "like",
        "orderType": "desc",
        "termMap": {
          "isTop": true
        }
      }
      axios.post(`/api/biliComment/getDocument`, topRequest)
          .then(response => {
            // 请求成功的处理逻辑
            searchBus.emit('updateTopCommentData', response.data);
          })
          .catch(error => {
            // 请求失败的处理逻辑
            console.error(error);
          });
    }
  }
}
</script>