<template>
  <div>
    <div ref="mapbox" class="box"></div>
  </div>
</template>

<script>
import echarts from "echarts";
import jsonp from "jsonp";
import "echarts/map/js/china.js";

const option = {
  title: {
    text: "疫情地图",
    textStyle: { fontSize: 20 },
    subtext: "更多信息",
    subtextStyle: {
      fontSize: 18
    },
    sublink: "http://sinovision.net/newpneumonia.php"
  },
  series: [
    {
      name: "确诊人数",
      type: "map", //告诉echarts 渲染地图
      map: "china",
      label: {
        show: true, //显示各个省份名称
        fontSize: 8
      },
      itemStyle: {
        areaColor: "#fff", //区域的背景颜色
        borderColor: "blue"
      },
      emphasis: {
        //控制鼠标滑过时的高亮样式
        itemStyle: {
          areaColor: "#c7fffd"
        }
      },
      data: [],
      roam: true,
      zoom: 1.2 //控制地图的大小
    }
  ],
  visualMap: [
    {
      type: "piecewise",
      show: true,
      splitNumber: 6,
      pieces: [
        {
          min: 10000
        }, // 不指定 max，表示 max 为无限大（Infinity）。
        {
          min: 1000,
          max: 9999
        },
        {
          min: 100,
          max: 999
        },
        {
          min: 10,
          max: 99
        },
        {
          min: 1,
          max: 9
        },
        {
          min: 0,
          max: 0
        }
      ],
      //align:'right' // 默认是left
      inRange: {
        symbol: "rect",
        color: ["#fff", "#ffaa85", "#ff7b69", "#cc2929", "#8c0d0d", "#660208"]
      },
      itemHeight: 10,
      itemWidth: 20
    }
  ],
  tooltip: {
    // position: function(pos, params, dom, rect, size) {
    //   // 鼠标在左侧时 tooltip 显示到右侧，鼠标在右侧时 tooltip 显示到左侧。
    //   var obj = { top: 60 };
    //   obj[["left", "right"][+(pos[0] < size.viewSize[0] / 2)]] = 5;
    //   return obj;
    // },
  }
};
export default {
  name: "HelloWorld",
  mounted() {
    this.getData();
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option);
  },
  methods: {
    getData() {
      jsonp(
        "http://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427",
        {},
        (err, data) => {
          if (!err) {
            var list = data.data.list.map(item => ({
              name: item.name,
              value: item.value
            }));
            option.series[0].data = list;
            this.mychart.setOption(option); //数据更新
          }
        }
      );
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.box {
  width: 100vw;
  height: 800px;
  margin: auto;
  /* border: 2px solid aquamarine; */
}
</style>
