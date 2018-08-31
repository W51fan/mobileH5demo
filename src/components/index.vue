<template>
  <div>
    <div v-if="isShowIndex" class="indexWarp">
      <div style="margin-top: 30%;font-size: 36pt;font-weight: 400;color: rgba(0, 255, 255, 0.8);">{{ title }}</div>
      <div style="font-size: 36pt;font-weight: 400;color: rgba(0, 255, 255, 0.8);">{{ subTitle }}</div>
      <div style="font-size: 20pt;font-weight: 400;color: rgb(102, 173, 173);margin-top: 8%;">体验版</div>
      <div style="font-size: 14pt;font-weight: 400;color: white;margin-top: 20%;background: #00ffff5e;padding: 5%;margin: 21%;border-bottom-left-radius: 11%;border-top-right-radius: 1" @click="begin()">现在开始</div>
    </div>

    <div v-if="isShowTest">
      <Test @testEnd="showReport"></Test>
    </div>
    <div v-if="isShowReport">
      <Report :reportData="reportData"></Report>
    </div>
  </div>
</template>

<script>
import Test from '@/components/test'
import Report from '@/components/report'
export default {
  name: "index",
  components:{
    Test,
    Report
  },
  
  data() {
    return {
      title: "企业智能制造",
      subTitle: "成熟度测评",
      isShowIndex:true,
      isShowTest:false,
      isShowReport:false,
      reportData:[]
    };
  },
  methods: {
    begin() {
      this.isShowIndex = false;
      this.isShowTest =true;
      this.isShowReport = false;
    },
    showReport(e){
      this.reportData = e;
      this.isShowIndex = false;
      this.isShowTest =false;
      this.isShowReport = true;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>

<style>

</style>

