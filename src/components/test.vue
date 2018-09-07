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
            <div style="color:white;font-size: 24px;margin: 10px 0 0 0;">企业智能制造成熟度测评</div>
             <!-- <div class='wrap'>
                <transition-group  name="no-mode-fade">
                    <div style="color:rgba(241, 241, 241, 0.6);margin: 10px 0 0 0;font-size: 18px;">
                        <span>{{currentQuestion}}/20</span>
                        <span>{{questions.level1}}</span><span>/</span><span>{{questions.level2}}</span>
                    </div>
                    <div class="questionWarp">
                        <div class="answeritemWarp">
                            <div style="color: white;margin:0 10px;height: 75px;">{{currentQuestion}}、{{questions.question}}</div>
                            <div v-for="(item,index) in questions.answerLists" :key="index" class="questionItem" @click="selected(item)" v-bind:class="{activeItem:selectedItem===item.id,thirdItem:item.id==3}">
                                <div style="margin: 10px;display: table-cell;vertical-align: middle;padding: 0 12px;">
                                    <span>
                                        {{item.label}}
                                    </span>
                                </div>
                            
                            </div>
                        </div>
                    </div>
                </transition-group>
            </div> -->
          <div :class="{'fadeIn':isfadeIn,'fadeBack':isfadeBack}">
            <div style="color:rgba(241, 241, 241, 0.6);margin: 10px 0 0 0;font-size: 18px;">
              <span>{{currentQuestion}}/20</span>
              <span>{{questions.level1}}</span><span>/</span><span>{{questions.level2}}</span>
            </div>
            <div class="questionWarp">
                <div class="answeritemWarp">
                    <div style="color: white;margin:0 10px;height: 50px;">{{currentQuestion}}、{{questions.question}}</div>
                    <div v-for="item in questions.answerLists" :key="item.id" class="questionItem" @click="selected(item)" v-bind:class="{activeItem:selectedItem===item.id,thirdItem:item.id==3}">
                        <div style="margin: 10px;display: table-cell;vertical-align: middle;padding: 0 12px;">
                            <span>
                                {{item.label}}
                            </span>
                        </div>
                    
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
    activeItem: 0, //激活的选项
    selectedItem: 0, //选中的选项
    questions: [],
    currentQuestion: 1,
    currentLevel1: "",
    scoreArray: [], //维度一得分数组
    level1Array: [],
    level2label: [],
    level2value: [],
    answersArray: [], //保存答案
    showquestion: true,
    isfadeIn: false,
    isfadeBack:false
  }),
  methods: {
    selected(e) {
      this.activeItem = e.id;
      this.selectedItem = e.id;

      if (this.answersArray.length < this.currentQuestion) {
        this.scoreArray.push({
          value: this.selectedItem - 1,
          label: this.questions.level1
        });
        this.answersArray.push(this.selectedItem);
        this.level2label.push(this.questions.level2);
        this.level2value.push(((this.selectedItem - 1) / 5).toFixed(2) * 100);
      } else {
        this.scoreArray[this.currentQuestion - 1] = {
          value: this.selectedItem - 1,
          label: this.questions.level1
        };
        this.answersArray[this.currentQuestion - 1] = this.selectedItem;
        this.level2value[this.currentQuestion - 1] =
          ((this.selectedItem - 1) / 5).toFixed(2) * 100;
      }
      setTimeout(() => {
        this.swipe("Left");
      }, 100);
    },
    swipe(direction) {
      let $this = this;
      if (direction == "Left") {
        this.isfadeIn = true;
        if (this.currentQuestion == 20) {
          if (this.level1Array.indexOf(this.questions.level1) == -1) {
            this.level1Array.push(this.questions.level1);
          }
          this.scoreArray[this.currentQuestion - 1] = {
            value: this.activeItem == 0 ? 0 : this.activeItem - 1,
            label: this.questions.level1
          };
          this.activeItem = 0;
          if (
            this.answersArray.length < this.currentQuestion &&
            this.selectedItem == 0
          ) {
            this.answersArray.push(this.selectedItem);
            this.level2label.push(this.questions.level2);
            this.level2value.push(0);
          } else {
            this.selectedItem = this.answersArray[this.currentQuestion - 1];
            this.level2value[this.currentQuestion - 1] =
              (
                (this.selectedItem == 0 ? 0 : this.selectedItem - 1) / 5
              ).toFixed(2) * 100;
          }

          this.sendscoreArray();
        } else {
          this.currentQuestion++;
          this.currentLevel1 = this.questions.level1;
          if (
            this.answersArray.length < this.currentQuestion &&
            this.selectedItem == 0
          ) {
            this.scoreArray.push({
              value: 0,
              label: this.questions.level1
            });
          } else {
            this.scoreArray[this.currentQuestion - 1] = {
              value: this.selectedItem - 1,
              label: this.questions.level1
            };
          }

          if (this.level1Array.indexOf(this.questions.level1) == -1) {
            this.level1Array.push(this.questions.level1);
          }
          this.questions = Questions.data[this.currentQuestion - 1];
          this.activeItem = 0;
          this.selectedItem = 0;
          if (
            this.answersArray.length < this.currentQuestion &&
            this.selectedItem == 0
          ) {
            this.answersArray.push(this.selectedItem);
            this.level2label.push(this.questions.level2);
            this.level2value.push(0);
          } else {
            this.selectedItem = this.answersArray[this.currentQuestion - 1];
            this.level2value[this.currentQuestion - 1] =
              (
                (this.selectedItem == 0 ? 0 : this.selectedItem - 1) / 5
              ).toFixed(2) * 100;
          }
        }
        setTimeout(() => {
          $this.isfadeIn = false;
        }, 200);
      } else {
        this.isfadeBack = true;
        if (this.currentQuestion == 1) return;
        this.activeItem = 0;
        this.currentQuestion--;
        this.selectedItem = this.answersArray[this.currentQuestion - 1];
        this.questions = Questions.data[this.currentQuestion - 1];

        this.scoreArray[this.scoreArray.length - 1] =
          this.selectedItem == 0 ? 0 : this.selectedItem - 1;

        this.level2value[this.currentQuestion - 1] =
          ((this.selectedItem == 0 ? 0 : this.selectedItem - 1) / 5).toFixed(
            2
          ) * 100;
        setTimeout(() => {
          $this.isfadeBack = false;
        }, 200);
      }
    },
    sendscoreArray() {
      console.log(this.scoreArray);
      let $this = this;
      let level1score = [];
      let level1 = this.scoreArray[0].label;
      let value = 0;
      this.scoreArray.forEach((item, index) => {
        if (item.label == level1) {
          value = value + item.value;
        } else {
          level1 = item.label;
          level1score.push(value);
          value = item.value;
          if (index == $this.scoreArray.length - 1) level1score.push(value);
        }
      });
      console.log(level1score);
      this.$emit("testEnd", [
        level1score,
        this.level1Array,
        this.level2label,
        this.level2value
      ]);
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
  background-size: 100% 100%;
  width: 99%;
  height: 100%;
  background-position: 0, 0;
  margin: 0 0.5%;
}

.activeItem {
  background-image: url(/static/imgs/bg_list_highlight@2x.png);
}
.activeItem div {
  color: #5affff !important;
}

.thirdItem {
  position: relative;
}

.pop-out-enter-active,
.pop-out-leave-active,
.pop-in-enter-active,
.pop-in-leave-active {
  will-change: transform;
  transition: all 500ms;
  height: 100%;
  top: 0;
  position: absolute;
  backface-visibility: hidden;
  perspective: 1000;
}
</style>

<style>
@media screen and (-webkit-min-device-pixel-ratio: 1) {
  .questionWarp {
    background-image: url(/static/imgs/bg_list.png);
    background-repeat: no-repeat;
    background-size: 100% 118%;
    padding: 15px 10px 25px 10px;
    background-position: bottom;
    width: 100%;
    height: 100%;
  }
  .questionItem {
    min-height: 60px;
    margin: 5px;
    cursor: pointer;
    color: #007878;
    background-image: url(/static/imgs/bg_list_normal@2x.png);
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 97%;
    display: table;
    height: 80px;
  }
  .answeritemWarp {
    text-align: left;
    width: 100%;
    height: 100%;
    font-size: 18px;
  }
}
@media screen and (min-width: 414px) {
  .questionWarp {
    background-image: url(/static/imgs/bg_list.png);
    background-repeat: no-repeat;
    background-size: 100% 118%;
    padding: 15px 10px 25px 10px;
    background-position: bottom;
    width: 100%;
    height: 100%;
  }
  .questionItem {
    min-height: 60px;
    margin: 5px;
    cursor: pointer;
    color: #007878;
    background-image: url(/static/imgs/bg_list_normal@2x.png);
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 97%;
    display: table;
    height: 80px;
  }
  .answeritemWarp {
    text-align: left;
    width: 100%;
    height: 100%;
    font-size: 18px;
  }
}

@media screen and (max-width: 375px) {
  .questionWarp {
    background-image: url(/static/imgs/bg_list.png);
    background-repeat: no-repeat;
    background-size: 100% 118%;
    padding: 15px 10px 25px 10px;
    background-position: bottom;
    width: 100%;
    height: 100%;
  }
  .questionItem {
    min-height: 60px;
    margin: 5px;
    cursor: pointer;
    color: #007878;
    background-image: url(/static/imgs/bg_list_normal@2x.png);
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 97%;
    display: table;
    height: 80px;
  }
  .answeritemWarp {
    text-align: left;
    width: 100%;
    height: 100%;
    font-size: 18px;
  }
}

@media screen and (max-width: 320px) {
  .questionWarp {
    background-image: url(/static/imgs/bg_list.png);
    background-repeat: no-repeat;
    background-size: 100% 118%;
    padding: 15px 10px 25px 10px;
    background-position: bottom;
    width: 100%;
    height: 100%;
  }
  .questionItem {
    min-height: 60px;
    margin: 5px;
    cursor: pointer;
    color: #007878;
    background-image: url(/static/imgs/bg_list_normal@2x.png);
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 97%;
    display: table;
    height: 100%;
  }
  .answeritemWarp {
    text-align: left;
    width: 100%;
    height: 100%;
    font-size: 16px;
  }
  .layout {
    display: block !important;
  }
}

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

/* .fade {
  -webkit-transition: opacity 1s ease-in-out;
  -moz-transition: opacity 1s ease-in-out;
  -o-transition: opacity 1s ease-in-out;
  transition: opacity 1s ease-in-out;
} */

@keyframes fade-in {
  0% {
    opacity: 0;
    transform: translateX(300px);
  } /*初始状态 透明度为0*/
  100% {
    opacity: 1;
    transform: translateX(0px);
  } /*结束状态 透明度为1*/
}
@-webkit-keyframes fade-in {
  /*针对webkit内核*/
  0% {
    opacity: 0;
    transform: translateX(300px);
  }
  100% {
    opacity: 1;
    transform: translateX(0px);
  }
}

.fadeIn {
  animation: fade-in; /*动画名称*/
  animation-duration: 0.2s; /*动画持续时间*/
  -webkit-animation: fade-in 0.2s; /*针对webkit内核*/
  animation-timing-function:linear;
}

@keyframes fade-back {
  0% {
    opacity: 0;
    transform: translateX(-300px);
  } /*初始状态 透明度为0*/
  100% {
    opacity: 1;
    transform: translateX(0px);
  } /*结束状态 透明度为1*/
}
@-webkit-keyframes fade-back {
  /*针对webkit内核*/
  0% {
    opacity: 0;
    transform: translateX(-300px);
  }
  100% {
    opacity: 1;
    transform: translateX(0px);
  }
}
.fadeBack {
  animation: fade-back; /*动画名称*/
  animation-duration: 0.2s; /*动画持续时间*/
  -webkit-animation: fade-back 0.2s; /*针对webkit内核*/
}
</style>

