<template>
  <div class="index">
    <!-- 头部搜索 -->
    <div class="search">
      <div @click="toMappage">{{cityName}}</div>
      <div>
        <input type="text" placeholder="搜索商品">
        <span class="icon"></span>
      </div>
    </div>
    <div class="swiper">
      <swiper class="swiper-container" indicator-dots="true" autoplay="true" interval="3000" circular="true" duration="500">
        <block v-for="(item, index) in banner" :key="index">
          <swiper-item class="swiper-item">
            <image class="slide-image" :src="item.iamge_url"/>
          </swiper-item>
        </block>
      </swiper>
    </div>
  </div>
</template>

<script>
import amapFile from '../../utils/amap-wx.js'
import { mapState, mapMutations } from 'vuex'
import { get } from '../../utils/index.js'
export default {
  data () {
    return {
      banner: []
    }
  },
  methods: {
    ...mapMutations(['update']),
    toMappage () {
      // 通过wx.getSetting 先查询一下用户是否授权 “scoped.record”
      let _this = this
      wx.getSetting({
        success: (res) => {
          // 如果没有同意授权，打开设置
          console.log(res)
          if (!res.authSetting['scope.userLocation']) {
            wx.openSetting({
              success: res => {
                // 获取位置授权位置信息
                _this.getCityName()
              }
            })
          } else {
            wx.navigateTo({
              url: '/pages/mappage/main'
            })
          }
        },
        fail: (err) => {
          console.log(err, 'err')
        },
        complete: () => {}
      })
    },
    getCityName () {
      let _this = this
      var myAmapFun = new amapFile.AMapWX({key:'01802cda5823471632303777658707e0'});
      myAmapFun.getRegeo({
        success: function (data) {
          // 成功回调
          console.log(data, 'data')
        },
        fail: function (info) {
          // 失败回调
          console.log(info, 'info')
          // _this.cityName = '北京'
          _this.update({cityName: '北京'})
        }
      })
    },
    async getData () {
      const data = await get('/index/index')
      console.log(data)
    }
  },
  computed: {
    ...mapState(['cityName'])
  }
}
</script>

<style scoped lang="less">
@import './style.less';
</style>
