<template>
    <div class="containWarp">
        <v-layout
            v-touch="{
            left: () => swipe('Left'),
            right: () => swipe('Right')
            }"
            column
            align-center
            justify-center
        > 
            <div style="color:white;">企业智能制造成熟度测评</div>
            <div style="color:rgba(241, 241, 241, 0.6);margin: 20px 0 0 0;">
                <span>{{currentQuestion}}/20</span>
                <span>{{questions.level1}}</span><span>/</span><span>{{questions.level2}}</span>
            </div>
            <div class="questionWarp">
                <div style="text-align: left;width: 100%;height: 450px;">
                    <div style="color: white;margin:5px 10px;">{{currentQuestion}}、{{questions.question}}</div>
                    <div v-for="item in questions.answerLists" :key="item.id" class="questionItem" @click="selected(item)" v-bind:class="{activeItem:activeItem===item.id,thirdItem:item.id==3}">
                        <!-- <span  style="color: rgba(44, 178, 179, 1);">{{item.label}}</span> -->
                        <img style="position: absolute;width: 40px;height: 80px;left: -7%;top: -15%;" src="/static/imgs/ic_last_left_normal@2x.png" v-if="item.id==3" >
                        <img style="position: absolute;width: 40px;height: 80px;right: -7%;top: -15%;" src="/static/imgs/ic_last_right_normal@2x.png" v-if="item.id==3" >
                        <div style="margin: 10px;">
                            {{item.label}}
                        </div>
                    
                    </div>
                </div>
            </div>
        </v-layout>
    </div>
</template>

<script>
import Questions from "./questions.json";
export default {
  name: "test",
  data: () => ({
    activeItem: 0,
    currentScore:0,
    questions: [],
    currentQuestion: 1,
    currentLevel1:'',
    scoreArray: [],
    level1Array: []
  }),
  methods: {
    selected(e) {
      this.activeItem = e.id;
    },
    swipe(direction) {
      if (direction == "Left") {
        if (this.currentQuestion == 20) {
          this.scoreArray.push(this.activeItem == 0 ? 0 : this.activeItem - 1);
          if (this.level1Array.indexOf(this.questions.level1) == -1) {
            this.level1Array.push(this.questions.level1);
          }
          if(this.currentLevel1 == this.questions.level1){
                this.currentScore = this.activeItem-1 + this.currentScore;
                this.scoreArray.push(this.currentScore);
            }else{
                this.scoreArray.push(this.currentScore);
                this.scoreArray.push(this.activeItem-1);
            }
          this.activeItem = 0;
          this.sendscoreArray();
        } else {
          this.currentQuestion++;
          //假如level1相同就累加
            if(this.currentLevel1 == this.questions.level1){
                this.currentScore = (this.activeItem==0?0:this.activeItem-1) + this.currentScore;
            }else{
                this.currentLevel1 = this.questions.level1;
                this.scoreArray.push(this.currentScore);
                this.currentScore = 0;
                this.currentScore = (this.activeItem==0?0:this.activeItem-1) + this.currentScore;  
            }
          
          if (this.level1Array.indexOf(this.questions.level1) == -1) {
            this.level1Array.push(this.questions.level1);
          }
          this.questions = Questions.data[this.currentQuestion - 1];
          this.activeItem = 0;
        }
      } else {
        if (this.currentQuestion == 1) return;
        this.activeItem = 0;
        this.currentQuestion--;
        this.scoreArray.pop();
        this.questions = Questions.data[this.currentQuestion - 1];
      }
    },
    sendscoreArray() {
      this.$emit("testEnd", [this.scoreArray,this.level1Array]);
    }
  },
  mounted: function() {
    this.questions = Questions.data[this.currentQuestion - 1];
    this.currentLevel1 = this.questions.level1;
  }
};
</script>

<style>
</style>

<style scoped>
.containWarp {
  background-image: url("/static/imgs/bg_2.jpg") !important;
  background-repeat: no-repeat;
  background-size: cover;
  width: 100%;
  height: 600px;
  overflow: scroll;
  background-position: 0, 0;
}
.questionWarp {
  background-image: url(/static/imgs/bg_list.png);
  background-repeat: no-repeat;
  background-size: cover;
  padding: 15px 10px 25px 10px;
  background-position: 0, 0;
}
.questionItem {
  min-height: 60px;
  /* background-color: rgba(7, 218, 206, 0.3); */
  /* border: 1px solid black; */
  margin: 5px;
  cursor: pointer;
  /* -webkit-tap-highlight-color: transparent; */
  color: #007878;
  background-image: url(/static/imgs/bg_list_normal@2x.png);
  background-repeat: no-repeat;
  background-size: cover;
  background-size: 100% 100%;
}
.activeItem {
  /* background-color: blue; */
  background-image: url(/static/imgs/bg_list_highlight@2x.png);
}
.activeItem div {
  color: #5affff !important;
}

.thirdItem {
  position: relative;
}
/* .questionItem:active {
  background-color: white;
  transition: background-color 0.3s cubic-bezier(0.4, 0, 0.2, 1),
    box-shadow 0.4s cubic-bezier(0.25, 0.8, 0.25, 1);
  will-change: background-color, box-shadow;
  box-shadow: inset 0 5px 10px #b41313, inset 0 -5px 10px #b41313;
} */
</style>


