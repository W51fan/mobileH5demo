<template>
    <div class="containWarp">
        <h2 style="color:rgba(0, 255, 255, 0.8);margin-top: 20px;font-size: 24px;">企业智能制造成熟度测评报告</h2>
        <div style="color:rgba(0, 255, 255, 0.5);margin: 24px 0;font-size: 16px;">
            <span>企业名称：</span><span>{{this.reportData[4].enterpriseName}}</span>
        </div>
        <div style="margin: 15px 0;font-size: 18px;">
            <span style="color:rgba(0, 255, 255, 0.5);">你的企业智能制造成熟度指数为</span><span style="color:red;font-size: 26px;">{{score}}</span><span style="color:rgba(0, 255, 255, 0.5);">%</span>
        </div>

        <h2 style="color:rgba(0, 255, 255, 0.8);margin: 30px 0">综合分析</h2>
        <div style="text-align: center;">
            <div id="chartOption1" style="width:100%;height:300px"></div>
        </div>
        <h2 style="color:rgba(0, 255, 255, 0.8);margin: 30px 0">各项指标能力指数</h2>
            <div>
                <div v-for="item in indicatorArray" :key="item.label">
                    <div style="display: inline-flex;">
                         <div style="text-align: right;margin-top: 15px;width: 84px;">
                            <span style="color:rgba(0, 255, 255, 0.8)">{{item.label}}：</span>
                        </div>
                        <span style="color: yellow;font-size: 27pt;">{{Math.ceil(item.value/item.max*100)}}</span><span style="color: yellow;margin-top: 18px;">%</span>
                    </div>
                </div>
            </div>
            <div style="color:rgba(0, 255, 255, 0.8);margin-top: 30px;">您的企业智能制造成熟度击败了
                <span style="color:red;font-size: 27pt;">{{this.reportData[5].score}}</span>%的测评者
            </div>
        <h2 style="color:rgba(0, 255, 255, 0.8);margin: 30px 0">指标能力分析</h2>
        <div style="text-align: center;">
            <div id="chartOption2" style="width:100%;height:500px;margin-top: -60px;"></div>
        </div>

        <div style="color: rgba(241, 241, 241, 0.6);margin: 10px;">
            <div>更多测评以及更详尽的评测结果请访问</div>
            <div>华制诊断云</div>
            <div>zhenduan.ehzcloud.com</div>
        </div>
    </div>
</template>

<script>
export default {
  name: "report",
  props: ["reportData"],
  data: () => ({
    // enterpriseName: "xx信息技术公司",
    score: 0,
    indicatorArray: []
  }),
  mounted: function(params) {
    this.reportData[0].forEach((item, index) => {
      this.score = this.score + item;
      this.indicatorArray.push({
        label: this.reportData[1][index],
        value: item
      });
    });
    let indicatorArray = [];
    console.log(this.reportData);
    this.reportData[1].forEach(element => {
      if (
        element == "网络环境" ||
        element == "网络安全" ||
        element == "服务" ||
        element == "架构平台" ||
        element == "销售"
      ) {
        indicatorArray.push({ name: element, max: 5 });
      } else if (
        element == "设计" ||
        element == "设备" ||
        element == "战略与组织" ||
        element == "仓库"
      ) {
        indicatorArray.push({ name: element, max: 10 });
      } else {
        indicatorArray.push({ name: element, max: 35 });
      }
    });
    this.indicatorArray.forEach((item, index) => {
      if (
        item.label == "网络环境" ||
        item.label == "网络安全" ||
        item.label == "服务" ||
        item.label == "架构平台" ||
        item.label == "销售"
      ) {
        this.indicatorArray[index].max = 5;
      } else if (
        item.label == "设计" ||
        item.label == "设备" ||
        item.label == "战略与组织" ||
        item.label == "仓库"
      ) {
        this.indicatorArray[index].max = 10;
      } else {
        this.indicatorArray[index].max = 35;
      }
    });
    this.$nextTick(function() {
      this.mychart1 = this.$echarts.init(
        document.getElementById("chartOption1")
      );
      this.mychart1.setOption(
        this.createRadar1Option(indicatorArray, this.reportData[0]),
        true
      );

      this.mychart2 = this.$echarts.init(
        document.getElementById("chartOption2")
      );
      this.mychart2.setOption(
        this.createBarOption(this.reportData[2], this.reportData[3]),
        true
      );
      this.mychart1.resize();
      this.mychart2.resize();
    });

    window.addEventListener("resize", () => {
      this.mychart1.resize();
    });
    window.addEventListener("resize", () => {
      this.mychart2.resize();
    });
  },
  methods: {
    createRadar1Option(indicatorArray, Cdata) {
      let option = {
        // backgroundColor: "white",
        // legend: {
        //   top: 20,
        //   left: 20,
        //   data: legend,
        //   itemGap: 20,
        //   selectedMode: "multiple"
        // },
        // yAxis:{
        //     axisLine:{
        //         lineStyle:{
        //             color:"rgba(180, 180, 180, 0.3)"
        //         }
        //     }
        // },
        radar: [
          {
            indicator: indicatorArray,
            shape: "polygon",
            splitLine: {
              lineStyle: {
                color: [
                  "rgba(0, 255, 255, 0.1)",
                  "rgba(0, 255, 255, 0.2)",
                  "rgba(0, 255, 255, 0.3)",
                  "rgba(0, 255, 255, 0.4)",
                  "rgba(0, 255, 255, 0.5)",
                  "rgba(0, 255, 255, 0.6)"
                ].reverse()
              }
            },
            name: {
              textStyle: {
                color: "rgba(0, 255, 255, 0.8)"
              }
            },
            axisLine: {
              lineStyle: {
                color: "rgba(180, 180, 180, 0.3)"
              }
            },
            splitArea: {
              show: true,
              areaStyle: {
                color: [
                  "rgba(0, 255, 255, 0.1)",
                  "rgba(0, 255, 255, 0.2)",
                  "rgba(0, 255, 255, 0.3)",
                  "rgba(0, 255, 255, 0.4)",
                  "rgba(0, 255, 255, 0.5)",
                  "rgba(0, 255, 255, 0.6)"
                ].reverse()
              }
            },
            splitLine: {
              show: false
            },
          }
        ],
        series: [
          {
            name: "雷达图",
            type: "radar",
            itemStyle: {
              emphasis: {
                lineStyle: {
                  width: 4
                }
              }
            },
            data: [
              {
                value: Cdata,
                name: "实际",
                itemStyle: {
                  normal: {
                    color: "yellow"
                  }
                }
                // areaStyle: {
                //   normal: {
                //     color: "rgba(19, 173, 255, 0.3)"
                //   }
                // }
              }
            ]
          }
        ]
      };
      return option;
    },
    createBarOption(label, value) {
      let option = {
        xAxis: {
          name: "%",
          type: "value",
          axisLabel: {
            textStyle: {
              fontSize: 14,
              color: "rgba(0, 255, 255, 0.8)"
            }
          },
          splitLine: {
            show: true,
            lineStyle: {
              color: "rgba(180, 180, 180, 0.3)"
            }
          },
          max: 100,
          min: 0,
          nameLocation: "end",
          nameTextStyle: {
            padding: [0, 0, -28, 0],
            color: "rgba(0, 255, 255, 0.8)"
          }
        },
        yAxis: {
          type: "category",
          data: label,
          axisLabel: {
            textStyle: {
              fontSize: 14,
              color: "rgba(0, 255, 255, 0.8)"
            }
          },
          axisLine: {
            lineStyle: {
              color: "#ccc"
            }
          },
          interval: 0
        },
        grid: {
          x: 100,
          x2: 30
        },
        series: [
          {
            name: "第二维度",
            type: "bar",
            data: value,
            itemStyle: {
              color: new this.$echarts.graphic.LinearGradient(1, 0, 0, 0, [
                {
                  offset: 1,
                  color: "rgba(0, 255, 255,0.2)"
                },
                // {
                //   offset: 0.5,
                //   color: "rgba(0, 255, 255, 0.5)"
                // },
                {
                  offset: 0,
                  color: "rgba(0, 255, 255, 1)"
                }
              ])
            }
          }
        ]
      };
      return option;
    }
  }
};
</script>


<style scoped>
.containWarp {
  background-image: url("/static/imgs/BImg.jpg") !important;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  width: 100%;
  height: 100%;
  background-position: 0, 0;
  /* z-index: -10; */
}
</style>

