<template>
  <view>
    <!--使用子组件-->
    <my-search-bar/>
    <!--轮播图-->
    <swiper indicator-dots autoplay circular :interval="3000">
      <navigator :url="'/subpkg/goods-detail/index?goods_id='+item.goods_id" v-for="item in swipers"
                 :key="item.good_id">
        <swiper-item>
          <image :src="item.image_src"></image>
        </swiper-item>
      </navigator>
    </swiper>
    <!--菜单数据-->
    <view class="nav-list">
      <image v-for="item in catitems" @click="switchTab(item.name)" :key="item.name" :src="item.image_src"
             class="nav-img" mode="scaleToFill"></image>
    </view>
    <!--楼层数据-->
    <view v-for="item in floordata" :key="item.floor.title.name">
      <!--头部部分-->
      <view>
        <image class="floor-title" :src="item.floor_title.image_src" model="scaleToFill"></image>
      </view>
      <!--身体部分-->
      <view class="floor-img-box">
        <!--  左边  -->
        <navigator :url="item.product_list[0].navigator_url">
          <image :src="item.product_list[0].image_src" :style="{width:item.product_list[0].image_width+'rpx'}"
                 mode="widthFix"></image>
        </navigator>
        <!--  右边 只显示4个，用v-show来判断条件 -->
        <view class="right-img-box">
          <navigator :url="subitem.navigator_url" v-for="(subitem,index) in item.product_list" :key="subitem.name">
            <image :src="subitem.image_src" v-show="index > 0" :style="{width:subitem.image_width+'rpx'}"
                   mode="widthFix"></image>
          </navigator>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
import tabBarBadge from "@/mixins/tabBar-badge";

export default {
  mixins: [tabBarBadge],
  data() {
    return {
      swipers: [],//轮播图数据
      catitems: [],//菜单数据
      floordata: []//楼层数据
    }
  },
  onLoad() {
    this.getSwipersData()
    this.getCatitemsData()
    this.getFloorData()
  },
  methods: {
    async getSwipersData() {
      const {message, meta: {status}} = await uni.$request({
        url: 'home/swiperdata',
      })
      if (status === 200) {
        this.swipers = message
      }
    },
    async getCatitemsData() {
      const {message, meta: {status}} = await uni.$request({
        url: 'home/catitems',
      })
      if (status === 200) {
        this.catitems = message
      }
    },
    async getFloorData() {
      const {message, meta: {status}} = await uni.$request({
        url: 'home/floordata',
      })
      if (status === 200) {
        message.forEach(item => {
          item.product_list.forEach(subitem => {
            subitem.navigator_url = '/subpkg/goods-list/index?' + subitem.navigator_url.split('?')[1]
          })
        })
        this.floordata = message
      }
    },
    //切换到tab
    switchTab(name) {
      if (name !== '分类') return
      uni.switchTab({
        url: '/pages/category/index'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.search-box {
  // 设置定位效果为“吸顶”
  position: sticky;
  // 吸顶的“位置”
  top: 0;
  // 提高层级，防止被轮播图覆盖
  z-index: 999;
}

swiper {
  height: 400rpx;

  .swiper-item,
  image {
    width: 100%;
    height: 100%;
  }
}

.nav-list {
  display: flex;
  justify-content: space-around;
  margin: 15px 0;

  .nav-img {
    width: 128rpx;
    height: 140rpx;
  }
}

.floor-title {
  height: 60rpx;
  width: 100%;
  display: flex;
}

.right-img-box {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

.floor-img-box {
  display: flex;
  padding-left: 10rpx;
}
</style>
