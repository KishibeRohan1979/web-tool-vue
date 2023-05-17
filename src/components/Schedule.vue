<template>
  <div>爬取进度：</div>
  <div class="xcprogress">
    <div class="xcprogress-bar" :style="{ 'width': `59%` }">
      <span id="xcprogress-bar-value" class="xcprogress-bar-value">59%</span>
    </div>
  </div>
  <xc-progress :value="value" @change="change"></xc-progress>
  <div id="feed-roll-btn" class="feed-roll-btn">

  </div>
</template>

<script>
export default {
  data() {
    return {
      value: 15
    }
  },
  methods: {
    change(value) {
      this.value = value;
    },
    mounted() {
      setTimeout(() => {
        setInterval(() => {
          this.value += 20;
        }, 800);
      }, 1000);
    }
  },
  watch: {
    value(newVal) {
      if (newVal > 100) {
        this.value = 100;
      } else if (newVal < 0) {
        this.value = 0;
      }
      this.$emit('change', this.value);
    }
  }
}
</script>