<template>
  <div class="reply-view-image" data-v-76b724b7="" v-show="isShow">
    <!-- 操作区 -->
    <div class="operation-btn">
      <div class="operation-btn-icon close-container" @click="closeBigImage">
        <i class="svg-icon close use-color" style="width: 14px; height: 14px;">
          <svg width="10" height="10" viewBox="0 0 10 10" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path fill-rule="evenodd" clip-rule="evenodd"
                  d="M0.284996 0.286944C0.480258 0.091682 0.796841 0.0916819 0.992103 0.286944L4.99893 4.29377L9.00684 0.285851C9.20211 0.0905889 9.51869 0.0905886 9.71395 0.285851C9.90921 0.481113 9.90921 0.797696 9.71395 0.992958L5.70603 5.00088L9.71309 9.00793C9.90835 9.20319 9.90835 9.51978 9.71309 9.71504C9.51783 9.9103 9.20124 9.9103 9.00598 9.71504L4.99893 5.70798L0.992966 9.71394C0.797704 9.90921 0.481122 9.90921 0.28586 9.71394C0.0905973 9.51868 0.0905975 9.2021 0.28586 9.00684L4.29182 5.00088L0.284996 0.994051C0.0897343 0.798789 0.0897342 0.482206 0.284996 0.286944Z"
                  fill="#E19C2C">
            </path>
          </svg>
        </i>
      </div>
      <div class="operation-btn-icon last-image" @click="lastImage">
        <i class="svg-icon left-arrow use-color" style="width: 22px; height: 22px;">
          <svg width="12" height="12" viewBox="0 0 12 12" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path fill-rule="evenodd" clip-rule="evenodd"
                  d="M7.67413 1.57564C7.90844 1.80995 7.90844 2.18985 7.67413 2.42417L4.09839 5.9999L7.67413 9.57564C7.90844 9.80995 7.90844 10.1899 7.67413 10.4242C7.43981 10.6585 7.05992 10.6585 6.8256 10.4242L3.00238 6.60094C2.67043 6.269 2.67043 5.73081 3.00238 5.39886L6.8256 1.57564C7.05992 1.34132 7.43981 1.34132 7.67413 1.57564Z"
                  fill="#A2A7AE">
            </path>
          </svg>
        </i>
      </div>
      <div class="operation-btn-icon next-image" @click="nextImage">
        <i class="svg-icon right-arrow use-color" style="width: 22px; height: 22px;">
          <svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path fill-rule="evenodd" clip-rule="evenodd"
                  d="M5.82576 2.07564C5.59145 2.30995 5.59145 2.68985 5.82576 2.92417L10.9015 7.9999L5.82576 13.0756C5.59145 13.31 5.59145 13.6899 5.82576 13.9242C6.06008 14.1585 6.43997 14.1585 6.67429 13.9242L11.9386 8.65987C12.3031 8.29538 12.3031 7.70443 11.9386 7.33994L6.67429 2.07564C6.43997 1.84132 6.06008 1.84132 5.82576 2.07564Z"
                  fill="#E19C2C">
            </path>
          </svg>
        </i>
      </div>
    </div>
    <!-- 图片内容 -->
    <div ref="bigImageDiv" class="show-image-wrap vertical">
      <img class="image-content" :src="getBigImageSrc()">
    </div>
    <!-- 小图预览栏 -->
    <div class="preview-list">
      <div v-for="(picture, index) in this.pictures" :class="previewClass(index)" @click="changeImageIndex(index)" style="width: 54px; height: 54px;">
        <div class="preview-item-wrap vertical">
          <img :src="picture.img_src + '@120w_120h_1c_!web-comment-note.webp'" >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import searchBus from "../assets/js/DataBus";

export default {
  data() {
    return {
      isShow: false,
      pictures: [],
      imageIndex: 0,
      bigImageDivSrc: ""
    }
  },
  methods: {
    // 显示class
    previewClass(index){
      if (index === this.imageIndex) {
        return "preview-item-box active"
      } else {
        return "preview-item-box"
      }
    },
    // 关闭大图预览
    closeBigImage() {
      this.isShow = false;
      this.pictures = [];
      this.imageIndex = 0;
      this.bigImageDivSrc = "";
    },
    // 获取大图地址
    getBigImageSrc() {
      if (this.pictures[this.imageIndex] === undefined) {
        return ""
      } else {
        this.bigImageDivSrc = this.pictures[this.imageIndex].img_src;
        return this.pictures[this.imageIndex].img_src;
      }
    },
    // 下一张
    nextImage() {
      this.imageIndex++;
      this.bigImageDivSrc = this.pictures[this.imageIndex].img_src;
    },
    // 上一张
    lastImage() {
      this.imageIndex--;
      this.bigImageDivSrc = this.pictures[this.imageIndex].img_src;
    },
    // 点击预览图切换大图
    changeImageIndex(index) {
      this.imageIndex = index;
      this.bigImageDivSrc = this.pictures[this.imageIndex].img_src;
    }
  },
  mounted() {
    searchBus.on("updateBigImage", (bigImage) => {
      this.isShow = true;
      this.pictures = bigImage.pictures;
      this.imageIndex = bigImage.index;
    });
  },
  watch: {
    imageIndex(newData) {
      if (newData < 0) {
        this.imageIndex = 0;
      }
      if (newData >= this.pictures.length) {
        this.imageIndex = this.pictures.length-1;
      }
    },
    // 让大图回到顶部，其他没什么用了
    bigImageDivSrc() {
      this.$refs.bigImageDiv.scrollTop = 0;
    }
  }
}
</script>