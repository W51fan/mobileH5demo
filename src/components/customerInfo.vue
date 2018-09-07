<template>
    <div class="containWarp">
        <div style="color:rgba(0, 255, 255, 0.8);margin: 64px 0 0 0;font-size: 16pt;">完善信息即刻</div>
        <div style="color:rgba(0, 255, 255, 0.8);font-size: 30pt;text-align: center;padding: 20px 0 30px 0;">查看测评报告</div>
        <div class="infoInputWarp">
            <div>您的姓名：</div>
            <input class="inputName  infoInput" v-model="name" type="text" maxlength="16" placeholder="请输入您的名字"/>
        </div>
        <div class="nameErrText">
            <div v-if="namehasErr">{{namehasErrText}}</div>
        </div>
        <div class="infoInputWarp">
             <div>联系电话：</div>
            <input class="inputName infoInput" v-model="mobile" type="text"  placeholder="请输入您的手机号码" v-on:change="checkMobile()"/>
        </div>
        <div class="mobileErrText">
            <div v-if="mobilehasErr">{{mobilehasErrText}}</div>
        </div>
        <div class="infoInputWarp">
            <span class="verifyCodeSpan">验证码：</span>
            <input class="inputName verifyCodeInput" v-model="verifyCode" type="text" maxlength="6" placeholder="请输入短信验证码" />
            <div class="vercodefont">
                <div v-if="!isGetCode" class="getVerCode" @click="getVerCode()" :class="{activeVerBtn:isGetCode}">获取验证码</div>
                <div v-else  class="getVerCode" :class="{activeVerBtn:isGetCode}">重新获取<span style="color:yellow">{{time}}</span>s</div>
            </div>
        </div>
        <div class="verCodeErrText">
            <div v-if="verCodehasErr">{{verCodehasErrText}}</div>
        </div>
        <div class="infoInputWarp">
             <div>企业名称：</div>
            <input class="inputName infoInput" v-model="enterpriseName" type="text" maxlength="16" placeholder="请输入您的企业名称"/>
        </div>
        <div class="enterpriseNameErrText">
            <div v-if="enterpriseNamehasErr">{{enterpriseNamehasErrText}}</div>
        </div>
        <div>
            <div class="sumbitBtn" :class="{activeBtn:isActive}" @click="sumbitInfo()">
                <span>提交</span>
            </div>
        </div>
         <div style="color:red;margin-left: -18px;">
            <div v-if="backendhasErr">{{backendhasErrText}}</div>
        </div>
    </div>
</template>

<script>
export default {
  name: "info",
  data: () => ({
    name: "",
    isActive: false,
    isGetCode: false,
    time: 0,
    counter: Object,
    mobile: "",
    verifyCode: "",
    enterpriseName: "",
    AlertMessage: "",
    showAlert: false,
    namehasErr: false,
    namehasErrText: "姓名不能为空",
    mobilehasErr: false,
    mobilehasErrText: "电话号码不能为空",
    verCodehasErr: false,
    verCodehasErrText: "验证码不能为空",
    enterpriseNamehasErr: false,
    enterpriseNamehasErrText: "企业名称不能为空",
    backendhasErr: false,
    backendhasErrText: ""
  }),
  methods: {
    getVerCode() {
      if (this.mobile == "") {
        this.mobilehasErr = true;
        return;
      }
      if (this.mobilehasErr) return;
      let $this = this,
        apikey = "",
        type = "post",
        url = " /IBUS/DAIG_SYS/send_sms_demo ",
        request = {
          mobile: this.mobile
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
            $this.verCodehasErr = true;
            $this.verCodehasErrText = "验证码错误";
          } else {
            this.isGetCode = !this.isGetCode;
            $this.beginCount(60);
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    sumbitInfo() {
      this.isActive = !this.isActive;
      if (this.name == "") {
        this.namehasErr = true;
        return;
      }
      if (this.mobile == "") {
        this.mobilehasErr = true;
        return;
      }
      if (this.verifyCode == "") {
        this.verCodehasErr = true;
        return;
      }
      if (this.enterpriseName == "") {
        this.enterpriseNamehasErr = true;
        return;
      }
      let $this = this,
        apikey = "",
        type = "post",
        url = " /IBUS/DAIG_SYS/register_user_demo ",
        request = {
          mobile: this.mobile,
          verifyCode: this.verifyCode,
          name: this.name,
          ent_name: this.enterpriseName
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
            $this.backendhasErr = true;
            $this.backendhasErrText = res.data.errorMsg;
          } else {
            $this.sendSorce();
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    countDown() {
      this.time--;
    },
    beginCount(num) {
      this.time = num;
      this.counter = setInterval(this.countDown, 1000);
    },
    isTelCode(str) {
      var reg = /^((0\d{2,3}-\d{7,8})|(1[3456789]\d{9}))$/;
      return reg.test(str);
    },
    sendSorce() {
      this.$emit("infoEnd", this.enterpriseName);
    },
    checkMobile() {
      this.mobilehasErr = false;
      //   if (this.mobile == "") {
      //     this.mobilehasErr = true;
      //     this.mobilehasErrText = "手机号码不能为空";
      //   } else
      if (!this.isTelCode(this.mobile)) {
        this.mobilehasErr = true;
        this.mobilehasErrText = "手机号码格式错误";
      }
    }
  },
  watch: {
    time: function(newValue, oldValue) {
      if (newValue == 0) {
        clearInterval(this.counter);
        this.isGetCode = false;
        this.time = 0;
      }
    },
    name: function(newValue, oldValue) {
      this.namehasErr = false;
      if (this.name == "") {
        this.namehasErr = true;
      }
    },
    mobile: function(newValue, oldValue) {
      this.mobilehasErr = false;
      if (this.mobile == "") {
        this.mobilehasErr = true;
        this.mobilehasErrText = "手机号码不能为空";
      }
      //else if (!this.isTelCode(this.mobile)) {
      //     this.mobilehasErr = true;
      //     this.mobilehasErrText = "手机号码格式错误";
      //   }
    },
    verifyCode: function(newValue, oldValue) {
      this.verCodehasErr = false;
      if (this.verifyCode == "") {
        this.verCodehasErr = true;
      }
    },
    enterpriseName: function(newValue, oldValue) {
      this.enterpriseNamehasErr = false;
      if (this.enterpriseName == "") {
        this.enterpriseNamehasErr = true;
      }
    }
  }
};
</script>

<style scoped>
.inputName:focus {
  border: none;
  border-bottom: 1px solid rgba(50, 80, 80, 1);
}
.getVerCode {
  background-image: url("/static/imgs/btn_list_highlight@2x.png") !important;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  width: 100%;
  height: 100%;
  background-position: 0, 0;
  padding: 5px;
  outline: none;
}
.activeVerBtn {
  background-image: url("/static/imgs/btn_list_normal@2x.png") !important;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  width: 100%;
  height: 100%;
  background-position: 0, 0;
  padding: 5px;
  outline: none;
}
.sumbitBtn {
  background-image: url("/static/imgs/btn_big_highlight@2x.png");
  background-repeat: no-repeat;
  background-size: 100% 100%;
  width: 84%;
  height: 100%;
  background-position: 0, 0;
  color: rgba(0, 255, 255, 0.8);
  outline: none;
  font-size: 14pt;
  padding: 24px 25px 28px 25px;
  font-family: sans-serif;
  margin: 20px 0;
  cursor: pointer;
  margin-left: 8%;
}
.activeBtn {
  background-image: url("/static/imgs/btn_big_normal@2x.png") !important;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  width: 84%;
  height: 100%;
  background-position: 0, 0;
  color: rgba(0, 255, 255, 0.8);
  outline: none;
  font-size: 14pt;
  padding: 24px 25px 28px 25px;
  font-family: sans-serif;
  margin: 20px 0;
  cursor: pointer;
  margin-left: 8%;
}
input::-webkit-input-placeholder,
textarea::-webkit-input-placeholder {
  /* WebKit browsers */
  color: #cccccc;
}
input:-moz-placeholder,
textarea:-moz-placeholder {
  /* Mozilla Firefox 4 to 18 */
  color: #cccccc;
}
input::-moz-placeholder,
textarea::-moz-placeholder {
  /* Mozilla Firefox 19+ */
  color: #cccccc;
}
input:-ms-input-placeholder,
textarea:-ms-input-placeholder {
  /* Internet Explorer 10+ */
  color: #cccccc;
}
</style>

<style>
@media screen and (-webkit-min-device-pixel-ratio: 1) {
  .containWarp {
    background-image: url("/static/imgs/bg_2.jpg") !important;
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 100%;
    height: 736px;
    overflow: scroll;
    background-position: 0, 0;
  }
  .vercodefont {
    font-size: 14px;
  }
  .verifyCodeInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 50%;
  }
  .infoInputWarp {
    color: rgba(0, 255, 255, 0.8);
    display: inline-flex;
    margin: 10px 0 30px 0;
    font-size: 18px;
    width: 80%;
  }
  .infoInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 70%;
  }
  .verifyCodeSpan {
    margin-top: 5px;
    width: 82px;
  }
  .nameErrText {
    color: red;
    margin-left: -60px;
  }
  .mobileErrText {
    color: red;
    margin-left: -38px;
  }
  .verCodeErrText {
    color: red;
    margin-left: -55px;
  }
  .enterpriseNameErrText {
    color: red;
    margin-left: -40px;
  }
}
@media screen and (min-width: 414px) {
  .containWarp {
    background-image: url("/static/imgs/bg_2.jpg") !important;
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 100%;
    height: 736px;
    overflow: scroll;
    background-position: 0, 0;
  }
  .vercodefont {
    font-size: 14px;
  }
  .verifyCodeInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 48%;
  }
  .infoInputWarp {
    color: rgba(0, 255, 255, 0.8);
    display: inline-flex;
    margin: 10px 0 30px 0;
    font-size: 18px;
    width: 80%;
  }
  .infoInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 70%;
  }
  .verifyCodeSpan {
    margin-top: 5px;
    width: 82px;
  }
  .nameErrText {
    color: red;
    margin-left: -60px;
  }
  .mobileErrText {
    color: red;
    margin-left: -38px;
  }
  .verCodeErrText {
    color: red;
    margin-left: -55px;
  }
  .enterpriseNameErrText {
    color: red;
    margin-left: -40px;
  }
}

@media screen and (max-width: 375px) {
  .containWarp {
    background-image: url("/static/imgs/bg_2.jpg") !important;
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 100%;
    height: 667px;
    overflow: scroll;
    background-position: 0, 0;
  }
  .vercodefont {
    font-size: 12px;
  }
  .verifyCodeInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 46%;
  }
  .infoInputWarp {
    color: rgba(0, 255, 255, 0.8);
    display: inline-flex;
    margin: 10px 0 30px 0;
    font-size: 16px;
    width: 80%;
  }
  .infoInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 70%;
  }
  .verifyCodeSpan {
    margin-top: 5px;
    width: 82px;
  }
  .nameErrText {
    color: red;
    margin-left: -50px;
  }
  .mobileErrText {
    color: red;
    margin-left: -28px;
  }
  .verCodeErrText {
    color: red;
    margin-left: -38px;
  }
  .enterpriseNameErrText {
    color: red;
    margin-left: -25px;
  }
}

@media screen and (max-width: 320px) {
  .containWarp {
    background-image: url("/static/imgs/bg_2.jpg") !important;
    background-repeat: no-repeat;
    background-size: 100% 100%;
    width: 100%;
    height: 568px;
    overflow: scroll;
    background-position: 0, 0;
  }
  .vercodefont {
    font-size: 12px;
  }
  .verifyCodeInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 43%;
  }
  .infoInputWarp {
    color: rgba(0, 255, 255, 0.8);
    display: inline-flex;
    margin: 10px 0 30px 0;
    font-size: 14px;
    width: 80%;
  }
  .infoInput {
    border-bottom: 1px solid rgba(50, 80, 80, 1);
    outline: none;
    width: 68%;
  }
  .verifyCodeSpan {
    margin-top: 5px;
    width: 67px;
  }
  .nameErrText {
    color: red;
    margin-left: -30px;
  }
  .mobileErrText {
    color: red;
    margin-left: -5px;
  }
  .verCodeErrText {
    color: red;
    margin-left: -20px;
  }
  .enterpriseNameErrText {
    color: red;
    margin-left: -5px;
  }
}
</style>

