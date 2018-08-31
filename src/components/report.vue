<template>
    <div class="containWarp">
        <h2 style="color:rgba(0, 255, 255, 0.8);">企业智能制造成熟度测评报告</h2>
        <div style="color:rgba(0, 255, 255, 0.5);margin: 10px 0;">
            <span>企业名称：</span><span>{{enterpriseName}}</span>
        </div>
        <div style="margin: 15px 0;">
            <span style="color:rgba(0, 255, 255, 0.5);">你的企业智能制造成熟度指数为</span><span style="color:red;font-size: 26px;">{{score}}</span><span style="color:rgba(0, 255, 255, 0.5);">%</span>
        </div>

        <h2 style="color:rgba(0, 255, 255, 0.8);margin: 30px 0">综合分析</h2>
        <div style="text-align: center;">
            <div id="chartOption1" style="width:100%;height:300px"></div>
        </div>
        <h2 style="color:rgba(0, 255, 255, 0.8);margin: 30px 0">各项指标能力指数</h2>

        <h2 style="color:rgba(0, 255, 255, 0.8);margin: 30px 0">指标能力分析</h2>
        

        <div style="color: rgba(241, 241, 241, 0.6);">
            <div>更多测评以及更详尽的评测结果请访问</div>
            <div>华制诊断云</div>
            <div>bianque.ehzcloud.com</div>
        </div>
    </div>
</template>

<script>
export default {
  name: "report",
  props: ["reportData"],
  data: () => ({
    enterpriseName: "xx信息技术公司",
    score: 0
  }),
  mounted: function(params) {
    let indicatorArray = [];
    console.log(this.reportData);
    this.reportData[1].forEach(element => {
        if(element =="网络环境" ||element =="网络安全"||element =="服务"||element =="架构平台"){
            indicatorArray.push({ name: element, max: 5 })
        }else if(element =="设计"||element =="设备"||element =="战略与组织"){
            indicatorArray.push({ name: element, max: 10 })
        }else if(element =="仓库"){
            indicatorArray.push({ name: element, max: 15 })
        }else{
            indicatorArray.push({ name: element, max: 35 })
        }
    });
    this.reportData[0].forEach(item =>{
        debugger
        this.score = this.score+item;
    })
    this.$nextTick(function() {
      this.mychart1 = this.$echarts.init(
        document.getElementById("chartOption1")
      );
      this.mychart1.setOption(
        this.createRadar1Option(indicatorArray, this.reportData[0]),
        true
      );
    });

    window.addEventListener("resize", () => {
      this.mychart1.resize();
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
        radar: [
          {
            indicator: indicatorArray,
            shape: "circle",
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
            splitArea: {
              show: false
            }
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
                    color: "blue"
                  }
                }
              }
            ]
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
  position: fixed;
  background-repeat: no-repeat;
  background-size: cover;
  width: 100%;
  height: 100%;
  /* z-index: -10; */
}
</style>

