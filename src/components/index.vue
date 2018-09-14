<template>
  <div class="indexbody">
    
    <div v-if="isShowIndex" class="indexWarp">
      
      <div class="bigTitle">{{ title }}</div>
      <div class="subTitle">{{ subTitle }}</div>
      <div style="font-size: 20pt;font-weight: 400;color: rgb(102, 173, 173);margin-top: 8%;">体验版</div>
      <div>
        <div class="beginBtn" :class="{beginBtnActive:isbeginBtnActive}"  @click="begin()">
          <span>现在开始</span>
        </div>
      </div>
      <img src="static/imgs/huazhilogo.png" class="huazhilogo">
    </div>
    

    <transition name="no-mode-fade">

      <div v-if="isShowTest">
        <Test @testEnd="showInfo"></Test>
      </div>

    </transition>

    <transition name="no-mode-fade">

      <div v-if="isShowReport">
        <Report :reportData="reportData"></Report>
      </div>

    </transition>

    <transition name="no-mode-fade">

      <div v-if="isShowInfo">
        <CustomerInfo @infoEnd="showReport"></CustomerInfo>
      </div>

    </transition>    
     

      

  </div>
</template>

<script>
import Test from "@/components/test";
import Report from "@/components/report";
import CustomerInfo from "@/components/customerInfo";
export default {
  name: "index",
  components: {
    Test,
    Report,
    CustomerInfo
  },
  data() {
    return {
      title: "企业智能制造",
      subTitle: "成熟度测评",
      isShowIndex: true,
      isShowTest: false,
      isShowReport: false,
      isShowInfo: false,
      isbeginBtnActive: false,
      reportData: [],
      loading: true
    };
  },
  methods: {
    begin() {
      this.isbeginBtnActive = true;
      setTimeout(() => {
        this.isShowIndex = false;
        this.isShowTest = true;
        this.isShowReport = false;
        this.isShowInfo = false;
      }, 200);
    },
    showInfo(e) {
      this.reportData = e;
      this.isShowIndex = false;
      this.isShowTest = false;
      this.isShowReport = false;
      this.isShowInfo = true;
    },
    showReport(e) {
      this.reportData.push({ enterpriseName: e });
      this.getdata();
    },
    getdata() {
      var score = 0;
      this.reportData[0].forEach((item, index) => {
        score = score + item;
      });
      let $this = this,
        apikey = "",
        type = "post",
        url = "  /IBUS/DAIG_SYS/statistic_data_demo",
        request = {
          score: score
        },
        param = {
          apikey,
          request
        };

      $this
        .$http({
          method: type,
          url: url,
          data: param
        })
        .then(res => {
          if (res.data.errorCode !== 0) {
          } else {
            console.log(res);
            $this.isShowIndex = false;
            $this.isShowTest = false;
            $this.isShowReport = true;
            $this.isShowInfo = false;
            $this.reportData.push({ score: res.data.return });
          }
        })
        .catch(error => {
          console.log(error);
        });
    }
  },
  mounted: function() {
    // document.querySelector("indexbody").addEventListener("touchmove", function(e) {
    //   // e.preventDefault();
    // });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.beginBtn {
  background-image: url("/static/imgs/btn_big_highlight@2x.png");
  background-repeat: no-repeat;
  background-size: 100% 100%;
  width: 84%;
  height: 100%;
  background-position: 0, 0;
  color: white;
  outline: none;
  font-size: 14pt;
  padding: 24px 25px 28px 25px;
  font-family: sans-serif;
  cursor: pointer;
  font-weight: 400;
  margin-top: 10%;
  text-align: center;
  margin-left: 8%;
}
.beginBtnActive {
  background-image: url("/static/imgs/btn_big_normal@2x.png") !important;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  width: 84%;
  height: 100%;
  background-position: 0, 0;
  color: white;
  outline: none;
  font-size: 14pt;
  padding: 24px 25px 28px 25px;
  font-family: sans-serif;
  cursor: pointer;
  font-weight: 400;
  margin-top: 10%;
  text-align: center;
  margin-left: 8%;
}
</style>

<style>
@media screen and (-webkit-min-device-pixel-ratio: 1) {
  .bigTitle {
    font-size: 40pt;
    margin-top: 36%;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .subTitle {
    font-size: 40pt;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .huazhilogo{
    z-index: 99999;width: 160px;margin-top:20%;
  }
}
@media screen and (min-width: 414px) {
  .bigTitle {
    font-size: 40pt;
    margin-top: 36%;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .subTitle {
    font-size: 40pt;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .huazhilogo{
    z-index: 99999;width: 160px;margin-top:20%;
  }
}

@media screen and (max-width: 375px) {
  .bigTitle {
    font-size: 36pt;
    margin-top: 36%;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .subTitle {
    font-size: 36pt;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .huazhilogo{
    z-index: 99999;width: 160px;margin-top:15%;
  }
}

@media screen and (max-width: 320px) {
  .bigTitle {
    font-size: 32pt;
    margin-top: 34%;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .subTitle {
    font-size: 32pt;
    font-weight: 400;
    color: rgba(0, 255, 255, 0.8);
  }
  .huazhilogo{
    z-index: 99999;width: 120px;margin-top:10%;
  }
}
/* .loading-overlay {
  width: 100%;
  height: 100%;
  background-color: #000;
  z-index: 9999;
}

.loading-overlay img {
  width: 10rem;
  height: 10rem;
  margin: auto;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
} */
.no-mode-fade-enter-active,
.no-mode-fade-leave-active {
  transition: all 0.3s;
}
.no-mode-fade-enter,
.no-mode-fade-leave-active {
  opacity: 0;
}
.no-mode-fade-enter {
  transform: translateX(20px);
}
.no-mode-fade-leave-active {
  transform: translateX(-20px);
}
</style>

