<template>
  <form action="#">
    <button type="button" class="cleardata-button">删除缓存</button>
    <input type="text" v-model.lazy="id" class="search-input" placeholder="输入BV号以爬取评论数据...">
    <input @click="getData" type="button" value="抓取" class="search-button">
  </form>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      id: ""
    }
  },
  methods: {
    getData() {
      axios.get(`/api/biliComment/getAsyncMsg?id=${this.id}`)
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