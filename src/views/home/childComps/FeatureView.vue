<template>
  <div class="feature">
    <div @click="gotocategory">
      <img src="~assets/img/home/recommend_bg1.jpg" alt="">
    </div>
  </div>
</template>

<script>
  export default {
    name: "FeatureView",
    methods:{
    gotocategory(){
      this.$router.replace('/category')
    }
    }
  }
</script>

<style scoped>
  .feature img {
    width: 100%;
  }
</style>
