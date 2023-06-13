<template>
  <view>
    <view class="scroll-view-container">
      <scroll-view  class="left-scroll-view" scroll-y="true" :style="{height:wh +'px'}">
        <block v-for="(item, i) in cateList" :key="i">
          <view :class = "['left-scroll-view-item',i === active? 'active' : '']" @click="activeChanged(i)">{{item.cat_name}}</view>
        </block>
      </scroll-view>  
      <scroll-view  scroll-y="true" :style="{height:wh +'px'}" :scroll-top="scrollTop">
        <view class="cate-lv2-box">
          <view class="cate-lv2-item" v-for="(item,i) in cateLevel2" :key="i">
            <view class="cate-lv2-title">{{item.cat_name}}</view> 
              <view class="cate-lv3-box">
                <view class="cate-lv3-item" v-for="(item3,i) in item.children" :key="i" @click="gotoGoodsList(item3)">
                  <image class="lv3-item-img" :src="item3.cat_icon"></image>
                  <view class="lv3-item-title">{{item3.cat_name}}</view>       
                </view>          
              </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        wh:0,
        active:0,
        scrollTop:0,
        cateList:[],
        cateLevel2:[]
      };
    },
    onLoad() {
      const sysInfo = uni.getSystemInfoSync()
      this.wh = sysInfo.windowHeight
      console.log(wh)
      this.getCateList()
    },
    methods:{
      async getCateList(){
        const {data:res} = await uni.$http.get('/api/public/v1/categories')
        if(res.meta.status !== 200) return uni.$showMsg()
        console.log(res)
        this.cateList = res.message
        this.cateLevel2 = res.message[0].children
      },
      activeChanged(i){
        this.active = i
        this.cateLevel2 = this.cateList[i].children
        this.scrollTop = this.scrollTop === 0 ? 1 : 0
        console.log(this.scrollTop)
      },
      gotoGoodsList(item3){
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?cid='+item3.cat_id
        })
      }
    }
  }
</script>
 
<style lang="scss">
  .scroll-view-container{
    display:flex;
    background-color: #ffffff;
    .left-scroll-view{
      width:120px;
      
      .left-scroll-view-item{
        line-height: 60px;
        background-color: #f7f7f7;
        text-align: center;
        font-size: 12px;
        
        &.active{
          background-color: #ffffff;
          position: relative;
          
          &::before{
            content: ' ';
            display: block;
            width: 3px;
            height: 30px;
            background-color: red;
            position: absolute;
            left:0px;
            transform:translateY(50%);
          }
        }
      }
    }
   }
    .cate-lv2-item{
      text-align: center;
    }
    .cate-lv2-title {
        font-size: 12px;
        font-weight: bold;
        text-align: center;
        padding: 15px 0;
    }
    .cate-lv3-box{
      display: flex;
      flex-wrap: wrap;
      
      .cate-lv3-item{
        display: flex;
        flex-direction: column;
        align-items: center;
        width: 33.33%;
        margin-bottom: 10px;
        .lv3-item-img{
          width: 60px;
          height: 60px;
        }
        .lv3-item-title{
          font-size: 12px;
        }
      }
    }
 

  
  
 
</style>
