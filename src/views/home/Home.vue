<template>
  <div id="home" class="wrapper">
    <nav-bar class="home-nav"><div slot="center">BEYOND</div></nav-bar>
     <tab-control 
                   :titles="['流行', '新款', '精选']"
                   @tabClick="tabClick" 
                   ref='tabControl1'
                   class="tab-control" v-show="isTabFixed"/>
    <scroll class="content"
            ref="scroll"
            :probe-type="3"
            @scroll="contentScroll"
            :pull-up-load="true"
            @pullingUp="loadMore">
      
      <swiper>
        <swiper-item> 
        <a href="https://fast.dewu.com/nezha-plus/detail/603380699882f7077424bb60"><img src="~assets/img/home/swiper1.jpg" alt=""></a></swiper-item>
        <swiper-item> 
        <a href="https://fast.dewu.com/nezha-plus/detail/60157c4e0eeb2c6932045dc9
"><img src="~assets/img/home/swiper2.jpg" alt=""></a></swiper-item>
        <swiper-item> 
        <a href="https://m.bzhm0z2c.cn/nezha-plus/33e6df92aaae9a0/6030e416ed843e077aa28be0"><img src="~assets/img/home/swiper3.jpg" alt=""></a></swiper-item>
        <swiper-item> 
        <a href="https://m.bzhm0z2c.cn/nezha-plus/aa5fdb37f868562/60307dfb5aee1f3bef889166
"><img src="~assets/img/home/swiper4.jpg" alt=""></a></swiper-item>
      </swiper>
      
      
      <feature-view></feature-view>
      <img src="~assets/img/home/TJ.jpg" alt="" class="TJ">
      <tab-control 
                   :titles="['流行', '新款', '精选']"
                   @tabClick="tabClick" 
                   ref='tabControl2' />
      <good-list :goods="showGoods"/>
     
    </scroll>
    <div></div>
    <back-top @click.native="backClick" v-show="isShowBackTop"/>
  </div>
</template>

<script>
import {Swiper,SwiperItem} from 'components/common/swiper'

  import RecommendView from './childComps/RecommendView'
  import FeatureView from './childComps/FeatureView'

  import NavBar from 'components/common/navbar/NavBar'
  import TabControl from 'components/content/tabControl/TabControl'
  import GoodList from 'components/content/goods/GoodsList'
  import Scroll from 'components/common/scroll/Scroll'
  import BackTop from 'components/content/backTop/BackTop'

  import { getHomeMultidata, getHomeGoods } from "network/home"


  export default {
    name: "Home",
    components: {
      RecommendView,
      FeatureView,
      NavBar,
      TabControl,
      GoodList,
      Scroll,
      BackTop,
      Swiper,
      SwiperItem
        
    },
    data() {
      return {
        banners: [],
        recommends: [],
        goods: {
          'pop': {page: 0, list: []},
          'new': {page: 0, list: []},
          'sell': {page: 0, list: []},
        },
        currentType: 'pop',
        isShowBackTop: false,
        taboffsetTop:0,
        isTabFixed: false,
        saveY :0
      }
    },
    computed: {
      showGoods() {
        return this.goods[this.currentType].list
      }
    },
    activated(){
      this.$refs.scroll.scrollTo(0,this.saveY,0)
      this.$refs.scroll.refresh()


    },
    deactivated(){
      this.saveY=this.$refs.scroll.getScrollY()
      // 取消全局事件监听
    },
    created() {
      // 1.请求多个数据
      this.getHomeMultidata()

      // 2.请求商品数据
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')
     
    },
    mounted() {
      this.$bus.$on('itemImageLoad',()=>{
        this.$refs.scroll.refresh()
      })
       this.taboffsetTop=this.$refs.TabControl

      
      

    },
    methods: {
      /**
       * 事件监听相关的方法
       */
      tabClick(index) {
        switch (index) {
          case 0:
            this.currentType = 'pop'
            break
          case 1:
            this.currentType = 'new'
            break
          case 2:
            this.currentType = 'sell'
            break
        }
        this.$refs.tabControl1.currentIndex=index;
        this.$refs.tabControl2.currentIndex=index;


      },
      backClick() {
        this.$refs.scroll.scrollTo(0, 0)
      },
      contentScroll(position) {
        this.isShowBackTop = (-position.y) > 1000
        this.isTabFixed=(-position.y) > this.taboffsetTop
      },
      loadMore() {
        this.getHomeGoods(this.currentType)
      },
      swiperImageLoad() {
        this.taboffsetTop=this.$refs.tabControl2.$el.offsetTop;

      },
      /**
       * 网络请求相关的方法
       */
      getHomeMultidata() {
        getHomeMultidata().then(res => {
          // this.result = res;
          this.banners = res.data.banner.list;
          this.recommends = res.data.recommend.list;
        })
      },
      getHomeGoods(type) {
        const page = this.goods[type].page + 1
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list)
          this.goods[type].page += 1

          this.$refs.scroll.finishPullUp()
        })
      }
    }
  }
</script>

<style scoped>
  #home{
  /* padding-top: 44px; */
  height: 100vh;
  position: relative;
}
.home-nav{
  background-color: #333333;
  color: #fff;
/* 
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  z-index: 9; */
}
.tab-control{
  position: relative;
  
  z-index: 9;
}
.content{
    overflow: hidden;

    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
}
 .TJ{
   width: 100%;
 }
</style>
