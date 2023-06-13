<template>
  <view>
    <!-- 轮播图的区域 -->
    <swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
      <swiper-item v-for="(item, i) in swiperList" :key="i">
        <navigator class="swiper-item" :url="'/subpkg/goods_detail/goods_detail?goods_id=' + item.goods_id">
          <image :src="item.image_src"></image>
        </navigator>
      </swiper-item>
    </swiper>
    <view>
        <view class="nav-list">
          <view class="nav-item" v-for="(item,i) in navList" :key="i" @click="navClickHandler(item)">
            <image :src="item.image_src"></image>
          </view>
        </view>
    </view>
    <view class="floor-list">
      <view class="floor-item" v-for="(item,i) in floorList" :key="i">
        <image class="floor-image" :src="item.floor_title.image_src"></image>
        <view class="floor-img-box">
          <navigator class="left-img-box" :url="item.product_list[0].url">
            <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width+'rpx'}" mode="widthFix"></image>
          </navigator> 
          <view class="right-img-box">
            <navigator class="right-img-item" v-for="(item2,i2) in item.product_list" :key="i2" :url="item2.url">
              <image v-if="i2 !== 0" :src="item2.image_src" :style="{width:item2.image_width+'rpx'}" mode="widthFix"></image>
            </navigator> 
          </view>
        </view>
      </view>
    </view>
</view>
</template>

<script>
  export default {
    data() {
      return {
        swiperList:[],
        navList:[],
        floorList:[]
      }
    },
    onLoad() {
      //调用方法，获取轮播图的数据
      this.getSwiperList()
      //分类导航列表
      this.getNavList()
      //获取楼层列表
      this.getFloorList()
    },
    methods:{
      async getSwiperList(){
        const {data:res} = await uni.$http.get('/api/public/v1/home/swiperdata')
        console.log(res)
        if(res.meta.status !== 200){
            return uni.$showMsg();
        }
        this.swiperList = res.message
      },
      async getNavList(){
        const {data:res} = await uni.$http.get('/api/public/v1/home/catitems')
        console.log(res)
        if(res.meta.status !== 200){
            return uni.$showMsg();
        }
        this.navList = res.message
      },
      async getFloorList(){
        const {data:res} = await uni.$http.get('/api/public/v1/home/floordata')
        console.log(res)
        if(res.meta.status !== 200){
            return uni.$showMsg();
        }
        //通过双层forEach循环，处理，URL地址
        res.message.forEach(floor =>{
          floor.product_list.forEach(prod =>{
            prod.url = '/subpkg/goods_list/goods_list?'+prod.navigator_url.split('?')[1]
          })
        })
        this.floorList = res.message
      },
      navClickHandler(item){
        console.log(item)
        if(item.name === '分类'){
          uni.switchTab({
            url:'/pages/cate/cate'
          })
        }
      },
    }
  }
</script>

<style lang="scss">
  swiper{
    height: 330rpx;
    .swiper-item,
    image{
      width: 100%;
      height: 100%;
    }
  }

  .nav-list{
    display: flex;
    justify-content: space-around;
    margin: 15rpx 0;
    .nav-item,
    image{
      height:128rpx;
      width: 140rpx;
    }
  }
  
  .floor-image{
    height: 60rpx;
    width: 100%;
    display: flex;
  }
  
  .right-img-box{
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  
  .floor-img-box{
    display: flex;
    padding: 5px;
  }
</style>
