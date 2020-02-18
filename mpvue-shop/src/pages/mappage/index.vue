<template>
  <div class="mappage">
    <div class="section">
      <input type="text" name="" id="" placeholder="搜索" focus="true" v-model="keywords" @input="bindInput">
    </div>
    <scroll-view :scroll-y="true" class="addcont" style="height: 500rpx">
      <div class="result" v-for="(item, index) in tips" :key="index" @touchstart="bindSearch(item.name)">
        {{item.name}}
      </div>
    </scroll-view>

    <div class="map_container">
      <div class="title">显示当前位置:</div>
      <map name="" class="map" id="map" scale="16" :longitude="longitude" :latitude="latitude" :markers="markers"></map>
    </div>
  </div>
</template>

<script>
import amapFile from '../../utils/amap-wx.js'
import { mapMutations } from 'vuex'
export default {
  data () {
    return {
      tips: [],
      longitude: 0,
      latitude: 0,
      markers: []
    }
  },
  mounted () {
    this.getMapaddress()
  },
  methods: {
    ...mapMutations(['update']),
    getMapaddress () {
      let _this = this
      var myAmapFun = new amapFile.AMapWX({
        key: '01802cda5823471632303777658707e0'
      })
      myAmapFun.getRegeo({
        iconPath: '/static/images/marker.png',
        iconWidth: 22,
        iconHeight: 32,
        success: function (data) {
          console.log(data, 'data')
          let maker = [
            {
              id: data[0].id,
              latitude: data[0].latitude,
              longitude: data[0].longitude,
              width: data[0].width,
              height: data[0].height
            }
          ]
          _this.makers = maker
          _this.longitude = data[0].longitude
          _this.latitude = data[0].latitude
        },
        fail: function (err) {
          console.log(err)
        }
      })
    },
    bindInput () {
      let _this = this
      let keywords = _this.keywords
      var myAmapFun = new amapFile.AMapWX({
        key: '01802cda5823471632303777658707e0'
      })
      myAmapFun.getInputtips({ // 高德地图搜索补全方法
        keywords: keywords,
        location: '',
        success: function (data) {
          console.log(data)
          if (data && data.tips) {
            _this.tips = data.tips
          }
        }
      })
    },
    bindSearch (cityName) {
      this.update({cityName: cityName})
      console.log(cityName, 'cityName')
      wx.navigateBack({
        delta: 1
      })
    }
  }
}
</script>

<style scoped lang="less">
@import './style.less';
</style>
