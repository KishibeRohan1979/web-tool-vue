<template>
  <div class="popup-container" id="popupContainer" v-show="isShowPop">
    <span class="close-button" @click="togglePopup()">X</span>
    <h2>可以多选哦</h2>
    <div class="checkbox-container">
      <input type="checkbox" v-model="checkbox" value="searchTime" id="searchTime">
      <label for="searchTime">根据时间单选:<br>
        <input type="radio" name="sort" value="1" v-model="sortValue"> 按最新到发布时间排序<br>
        <input type="radio" name="sort" value="2" v-model="sortValue"> 按发布时间到最近排序<br>
        <input type="radio" name="sort" value="3" v-model="sortValue"> 时间范围
        <div style="width: 380px">
          <VueDatePicker
              v-model="time"
              locale="zh-CN"
              :day-names="['一', '二', '三', '四', '五', '六', '七']"
              enable-seconds
              show-now-button
              now-button-label="Now"
              format="yyyy-MM-dd HH:mm:ss"
              cancelText="X"
              selectText="√"
              range :partial-range="false"
          />
        </div>
      </label>
    </div>
    <div class="checkbox-container">
      <input type="checkbox" v-model="checkbox" value="fuzzyQuery" id="fuzzyQuery">
      <label for="fuzzyQuery">完全模糊查询：
        <input type="text" v-model="fuzzyQueryText" placeholder="输入关键字">
      </label>
    </div>
    <div class="checkbox-container">
      <input type="checkbox" v-model="checkbox" value="fieldSearch" id="fieldSearch">
      <label for="fieldSearch">根据字段模糊查询:
        <select id="selectField" v-model="selectField">
          <option value="uname">用户名</option>
          <option value="thisUserMessage">评论内容</option>
          <option value="sign">个人简介</option>
        </select>
        <input type="text" v-model="fieldSearchText" placeholder="输入关键字">
      </label>
    </div>
    <div class="checkbox-container">
      <input type="checkbox" v-model="checkbox" value="fansOnly" id="fansOnly">
      <label for="fansOnly">只看老粉</label>
    </div>
    <div class="checkbox-container">
      <input type="checkbox" v-model="checkbox" value="badgeFans" id="badgeFans">
      <label for="badgeFans">只看有粉丝牌</label>
    </div>
    <a href="./src/html/setting.html" target="_blank" class="setting-button">设置</a>
    <button type="button" class="clear-button" @click="resetPopup">清空</button>
  </div>
</template>

<script>
import searchBus from "../assets/js/DataBus.js";
import VueDatePicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'

export default {
  data() {
    return {
      isShowPop: false,
      checkbox: [],
      sortValue: "",
      selectField: "uname",
      time: null,
      fuzzyQueryText: "",
      fieldSearchText: "",
    }
  },
  mounted() {
    searchBus.on("updateShowPop", (isShow) => {
      this.isShowPop = isShow;
    });
  },
  methods: {
    togglePopup() {
      this.isShowPop = false;
      searchBus.emit('updateShow', this.isShowPop);
      searchBus.emit('updateCheckbox', this.checkbox);
      searchBus.emit('updateSortValue', this.sortValue);
      searchBus.emit('updateSelectField', this.selectField);
      searchBus.emit('updateTime', this.time);
      searchBus.emit('updateFuzzyQueryText', this.fuzzyQueryText);
      searchBus.emit('updateFieldSearchText', this.fieldSearchText);
    },
    resetPopup() {
      this.checkbox = [];
      this.sortValue = "";
      this.selectField = "uname";
      this.time = null;
      this.fuzzyQueryText = "";
      this.fieldSearchText = "";
    }
  },
  components: {
    VueDatePicker
  },
  watch: {
    sortValue(newVal) {
      const itemToDelete = "searchTime";
      const indexOfItem = this.checkbox.indexOf(itemToDelete);
      if (indexOfItem === -1 && newVal !== "") {
        this.checkbox.push("searchTime");
      } else if (indexOfItem !== -1 && newVal === "") {
        this.checkbox.splice(indexOfItem, 1);
      }
    },
    fuzzyQueryText(newVal) {
      const itemToDelete = "fuzzyQuery";
      const indexOfItem = this.checkbox.indexOf(itemToDelete);
      if (indexOfItem === -1 && newVal !== "") {
        this.checkbox.push("fuzzyQuery");
      } else if (indexOfItem !== -1 && newVal === "") {
        this.checkbox.splice(indexOfItem, 1);
      }
    },
    fieldSearchText(newVal) {
      const itemToDelete = "fieldSearch";
      const indexOfItem = this.checkbox.indexOf(itemToDelete);
      if (indexOfItem === -1 && newVal !== "") {
          this.checkbox.push("fieldSearch");
      } else if (indexOfItem !== -1 && newVal === "") {
        this.checkbox.splice(indexOfItem, 1);
      }
    }
  }
}
</script>