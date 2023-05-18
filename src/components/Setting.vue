<template>
  <div id="search-container" class="search-container">
    <SearchContainer/>
  </div>
  <div id="schedule">
    <Schedule/>
  </div>
</template>

<script>
import Schedule from "./Schedule.vue";
import SearchContainer from "./SearchContainer.vue";

export default {
  data() {
    return {

    }
  },
  components: {
    Schedule,
    SearchContainer
  }
}
</script>