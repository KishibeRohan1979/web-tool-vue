<template>
  <form action="#">
    <button type="button" @click="clearData" class="cleared-button">删除缓存</button>
    <input type="text" v-model.lazy.trim="oid" class="search-input" placeholder="输入BV号以爬取评论数据...">
    <input @click="getData" :disabled="isDisabled" type="button" value="抓取" :class="searchButton">
  </form>
</template>

<script>
import axios from 'axios';
import settingBus from "../assets/js/DataBus";

export default {
  data() {
    return {
      oid: "",
      isDisabled: false,
      searchButton: "search-button",
      progress: 0
    }
  },
  methods: {
    getData() {
      const request = {
        "oid": this.oid,
        "type": "1"
      };
      axios.post(`/api/biliComment/addDoc`, request)
          .then(response => {
            // 请求成功的处理逻辑
            if (response.data.code === 0) {

              this.isDisabled = true;
              this.searchButton = "search-button-disabled";
              // 定义一个变量用于存储定时器的 ID
              let intervalId = null;
              const fetchAsyncMsg = () => {
                // 查询进度
                axios.get(`/api/biliComment/getAsyncMsg?id=${this.oid}`)
                    .then(response => {
                      // 请求成功的处理逻辑
                      this.progress = response.data.data.progress;
                      // 把数据送给进度条组件
                      this.changeProgress((response.data.data));
                      // 进度大于等于100的时候判断结束
                      if (this.progress >= 100) {
                        console.log(this.progress);
                        this.isDisabled = false;
                        this.searchButton = "search-button";
                        clearInterval(intervalId);
                      }
                    })
                    .catch(error => {
                      // 请求失败的处理逻辑
                      console.error(error);
                    });
              };
              // 每秒执行一次查询进度的方法
              intervalId = setInterval(fetchAsyncMsg, 1000);
            } else {
              alert("爬取失败！");
            }
          })
          .catch(error => {
            // 请求失败的处理逻辑
            console.error(error);
          });
    },
    // 把数据送给进度条组件
    changeProgress(data) {
      settingBus.emit('updateData', data);
      settingBus.emit('updateOid', this.oid);
    },
    clearData() {
      if (confirm("确认要进行删除缓存吗?")) {
        let oid = prompt("请输入对应缓存的BV号");
        if (oid != null) {
          oid = oid.trim();
          axios.get(`/api/biliComment/deleteIndex?bvid=${oid}`)
              .then(response => {
                console.log(response.data);
                alert(response.data.message);
              })
              .catch(error => {
                console.error(error);
              });
        }
      }
    }
  },
  watch: {
    oid() {
      settingBus.emit('updateOid', this.oid);
    }
  }
}
</script>