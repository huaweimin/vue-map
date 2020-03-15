<template>
  <div class="hello">
    <!-- 初始化echarts需要个有宽高的盒子 -->
    <div ref="mapbox" style="height:500px; width:1350px">

    </div>
  </div>
</template>

<script>
import echarts from 'echarts'
import 'echarts/map/js/china.js'
import jsonp from 'jsonp'

// 使用地图 需要先注册地图
const option = {
  title: {
    text: '疫情地图'
  },
  series: [{
    name: '确诊人数',
    type: 'map', // 告诉echarts 要去渲染的是一个地图
    map: 'china',// 告诉echarts 要去渲染的是中国地图
    label: {
      // 控制地区的汉字
      show: true,
      color: '#333', // 控制地区名的字体颜色
      fontSize: 10
    },
    itemStyle: {
      // 地图板块的边框和颜色
      areaColor: '#eee',
      // borderColor: 'blue'
    },
    roam: true,
    zoom: 1.2, //控制地图的缩放
    emphasis: {
      // 控制鼠标滑过之后的背景色和字体颜色
      label: {
          color: '#fff',
          fontSize: 12
      },
      itemStyle: {
        areaColor: '#83b5e7'
      }
    },
    data: [] // 用来展示后台给的数据
  }],
  visualMap: [{
    type: 'piecewise',
    show: true,
    pieces: [
      // 分段
      { min:1000 },
      { min:1000, max:9999 },
      { min:100, max:999 },
      { min:10, max:99 },
      { min:1, max:9 },
    ],
    inRange: {
      symbol: 'rect',
      color: ['#ffc0b1', '#9c0505']
    }
  }],
  tooltip: {
    trigger: 'item'
  }
}

export default {
  name: 'HelloWorld',
  mounted() {
    this.getData()
    this.mychart = echarts.init(this.$refs.mapbox)
    this.mychart.setOption(option)
  },
  methods: {
    getData() {
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json',{},(err,data)=>{
        if(!err) {
          // 请求数据成功
          console.log(data);
          let list = data.data.list.map(item => ({ name:item.name, value:item.value}))
          option.series[0].data = list
          this.mychart.setOption(option)
        }
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
