<template>
  <div class="container">
    <div class="top">
      <Header></Header>
    </div>
    <div class="mid">
      <div class="content">
        <el-steps :active="active" process-status="wait" align-center>
          <el-step title="填写账号" style="font-size:10px"></el-step>
          <el-step title="身份验证"></el-step>
          <el-step title="设置新密码"></el-step>
          <el-step title="完成"></el-step>
        </el-steps>

        <!-- 填写账号 -->
        <div class="personal-Name" v-if="active===1">
          <el-form
            :model="ruleForm"
            status-icon
            :rules="rules"
            ref="ruleForm"
            label-width="60px"
            class="demo-ruleForm"
          >
            <el-form-item prop="userName" label-width="30px">
              <span>手机号</span>
              <el-input type="text" v-model="ruleForm.userName" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <el-button class="btn1" @click="goHome">取消</el-button>
          <el-button class="btn2" @click="next('ruleForm')">下一步</el-button>
        </div>

        <!-- 邮箱/短信验证 -->
        <div class="personal-Info" v-if="active===2">
          <el-form
            :model="ruleForm"
            status-icon
            :rules="rules"
            ref="ruleForm"
            label-width="60px"
            class="demo-ruleForm"
          >
            <div class="Verification">
              <span v-bind:class="{active:tabType==1}" @click="tab(1)">邮箱验证</span>
              <span v-bind:class="{active:tabType==2}" @click="tab(2)">手机验证</span>
            </div>

            <el-form-item label="邮箱" prop="Email" v-if="tabType===1" class="Verification-info">
              <el-input type="text" v-model="ruleForm.Email" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="验证码" v-if="tabType===1" class="Verification-info">
              <div class="buttonItem">
                <input v-model="vercode" type="text" placeholder="输入验证码" />
                <div class="red sendCode" @click="sendMessage1">{{btnText1}}</div>
              </div>
            </el-form-item>

            <el-form-item label="手机" prop="Phone" v-if="tabType===2" class="Verification-info">
              <el-input type="text" disabled v-model="ruleForm.userName" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="验证码" v-if="tabType===2" class="Verification-info">
              <div class="buttonItem">
                <input v-model="vercode" type="text" placeholder="输入验证码" />
                <div class="red sendCode" @click="sendMessage2">{{btnText2}}</div>
              </div>
            </el-form-item>
          </el-form>
          <el-button @click="next('ruleForm')">下一步</el-button>
        </div>

        <!-- 设置新密码 -->
        <div class="personal-Pwd" v-if="active===3">
          <el-form
            :model="ruleForm"
            status-icon
            :rules="rules"
            ref="ruleForm"
            label-width="80px"
            class="demo-ruleForm"
          >
            <el-form-item label="新密码" prop="Pass">
              <el-input type="password" v-model="ruleForm.Pass" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="确认密码" prop="checkPass">
              <el-input type="password" v-model="ruleForm.checkPass" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <el-button @click="next('ruleForm')">下一步</el-button>
        </div>

        <!-- 设置成功 -->
        <div class="personal-Finish" v-if="active===4">
          <p>恭喜！密码重置成功！</p>
          <el-button @click="submitForm()">设置成功</el-button>
        </div>
      </div>
    </div>
  </div>

  <!-- <div class="find-password1">
        <div @click="next">下一步 => 2</div>
  </div>-->
</template>

<script>
import Header from "../components/SafetyCenter/Header";
export default {
  // name: "find-password1",
  data() {
    var NameValue = (rule, value, callback) => {
      if (!value) {
        return callback(new Error("账号不能为空"));
      }
    };
    var CheckPass = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value !== this.ruleForm.Pass) {
        callback(new Error("两次输入密码不一致!"));
      } else {
        callback();
      }
    };
    return {
      vercode: "",
      btnDisabled: false,
      btnText1: "获取验证码",
      btnText2: "获取验证码",
      disabled:false,
      yzm: "",
      active: 1,
      tabType: 1,
      ruleForm: {
        Name: "",
        Phone: "",
        Email: "",
        userName: "",
        Pass: "",
        checkPass: "",
        code: ""
      },
      rules: {
        Name: [
          {
            validator: NameValue,
            required: true,
            trigger: "blur"
          }
        ],
        Phone: [
          { required: true, trigger: "blur" },
          {
            pattern: /^1([38][0-9]|4[579]|5[0-3,5-9]|6[6]|7[0135678]|9[89])\d{8}$/,
            message: "手机格式不对"
          }
        ],
        Email: [
          { required: true, trigger: "blur" },
          {
            pattern: /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+((\.[a-zA-Z0-9_-]{2,3}){1,2})$/,
            message: "请输入有效的邮箱"
          }
        ],
        userName: [
          { required: true, message: "请输入手机号", trigger: "blur" },
          {
            pattern: /^1([38][0-9]|4[579]|5[0-3,5-9]|6[6]|7[0135678]|9[89])\d{8}$/,
            message: "手机格式不对"
          }
        ],
        Pass: [
          { required: true, message: "请输入密码", trigger: "blur" },
          { min: 6, max: 16, message: "密码长度为6-16个字符", trigger: "blur" }
        ],
        checkPass: [
          { required: true, validator: CheckPass, message: "", trigger: "blur" }
        ]
      }
    };
  },

  methods: {
    goHome(){
      this.$router.push("/Login");
    },
    next(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          if (this.active == 1) {
            this.$api.safety
              .judgeUser({ loginName: this.ruleForm.userName })
              .then(res => {
                if (res.success == true) {
                  this.active++;
                  console.log(this.active);
                } else if (res.success == false) {
                  this.$message.error("该账号不存在");
                }
              })
              .catch(err => console.log(err));
          } else if (this.active == 2) {
            if (this.vercode && this.yzm == this.vercode) {
              this.active++;
            } else {
              this.$message.error("您的验证码无效");
            }
          } else if (this.active == 3) {
            this.$api.safety
              .updatePwd({
                password: this.ruleForm.Pass,
                loginName: this.ruleForm.userName
              })
              .then(res => {
                if (res.success == true) {
                  this.active++;
                }
              });
          }
        } else {
          return false;
        }
      });
    },
    submitForm() {
      this.$router.push("/login").catch(err => console.log(err));
    },
    tab(index) {
      this.tabType = index;
    },
    // 验证码的点击事件
    sendMessage1() {
      if (this.btnDisabled) {
        return;
      }
      if (this.tabType === 1 && this.ruleForm.Email == "") {
        // alert("请您先输入邮箱账号");
         this.$notify({
          title: '警告',
          message: '请先输入邮箱账号',
          type: 'warning'
        })
        return;
      }
      //判断是手机验证还是邮箱验证
      if (this.tabType === 1) {
        this.$api.safety.sendEmail({ email: this.ruleForm.Email }).then(res => {
          console.log(res.emailCode);
          this.yzm = res.emailCode;
        });
      } else if (this.tabType === 2) {
        this.$api.safety
          .sendSms({ phone: this.ruleForm.userName })
          .then(res => {
            console.log(res.randomNum);
            this.yzm = res.randomNum;
          });
      }
      this.getSecond1(60);
    },
    sendMessage2() {
      if (this.btnDisabled) {
        return;
      }
      if (this.tabType === 1 && this.ruleForm.Email == "") {
        // alert("请您先输入邮箱账号");
         this.$notify({
          title: '警告',
          message: '请先输入邮箱账号',
          type: 'warning'
        })
        return;
      }
      //判断是手机验证还是邮箱验证
      if (this.tabType === 1) {
        this.$api.safety.sendEmail({ email: this.ruleForm.Email }).then(res => {
          console.log(res.emailCode);
          this.yzm = res.emailCode;
        });
      } else if (this.tabType === 2) {
        this.$api.safety
          .sendSms({ phone: this.ruleForm.userName })
          .then(res => {
            console.log(res.randomNum);
            this.yzm = res.randomNum;
          });
      }
      this.getSecond2(60);
    },
    //发送验证码
    getSecond1(wait) {
      let _this = this;
      let _wait = wait;

      if (wait == 0) {
        this.btnDisabled = false;
        this.btnText1 = "获取验证码";
        wait = _wait;
      } else {
        this.btnDisabled = true;
        this.btnText1 = "验证码(" + wait + "s)";
        wait--;
        setTimeout(function() {
          _this.getSecond1(wait);
        }, 1000);
      }
    },
    getSecond2(wait) {
      let _this = this;
      let _wait = wait;

      if (wait == 0) {
        this.btnDisabled = false;
        this.btnText2 = "获取验证码";
        wait = _wait;
      } else {
        this.btnDisabled = true;
        this.btnText2 = "验证码(" + wait + "s)";
        wait--;
        setTimeout(function() {
          _this.getSecond2(wait);
        }, 1000);
      }
    }
  },
  components: {
    Header
  }
};
</script>
<style>
.el-step__head {
  width: 150px;
}
/* 圆圈 */
.el-step__icon.is-text {
  width: 50px;
  height: 50px;
  background: rgba(255, 255, 255, 0);
}
.el-step__main {
  width: 150px;
}
.el-step__line {
  width: 100px;
  margin-top: 10px;
  line-height: 50px;
  margin-left: 25px;
  margin-right: 50px;
}
/* 文字 :填写账号*/
.el-step__title.is-finish {
  width: 150px;
}
.el-step__head.is-finish {
  width: 150px;
}
.el-step__icon {
  width: 100px;
}
.el-step__icon-inner {
  font-size: 36px;
}
.Verification-info .el-input__inner {
  width: 320px;
}
</style>
<style lang="scss" scoped>
.container {
  width: 100%;
  height: 100%;
  background-image: url("../assets/images/login/pic2.jpg");
  background-attachment: fixed;
  background-size: 100% 100%;

  .mid {
    width: 100%;
    height: calc(100% - 79px);
    display: flex;
    align-items: center;
    justify-content: center;

    .content {
      width: 800px;
      height: 500px;
      position: relative;
      background: rgba(255, 255, 255, 0.6);
      border-radius: 8px;

      // 步骤条
      .el-steps {
        position: absolute;
        width: 50%;
        top: 15%;
        left: 28%;
        transform: translate(-28%, -10%);
        // margin-top: 30px;
      }

      .el-form {
        width: 420px;
        padding: 20px 20px 20px 10px;
        background: rgba(255, 255, 255, 0.2);
        position: absolute;
        margin-top: 30px;
        top: 45%;
        left: 50%;
        transform: translate(-50%, -40%);
        border-radius: 10px;

        // 手机验证、邮箱验证-标题
        .Verification {
          width: 100%;
          text-align: center;
          margin-top: 14px;
          margin-bottom: 18px;
          
          span {
            color: black;
            font-size: 18px;
          }
          span:first-of-type {
            border-right: solid 1px #409EFF;
            padding-right: 18px;
            margin-right: 18px;
          }
          .active {
            color: #409EFF;
            font-weight: bold;
            font-size: 21px;
          }
        }
        // 手机验证、邮箱验证-内容
        .Verification-info {
          width: 400px;
          .el-input {
            width: 30px;
          }
          // 验证码样式的设置 start
          .buttonItem {
            border-radius: 5px;
            background-color: #fff;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 10px;
            border: 1px solid #ddd;
            width: 320px;
            input {
              width: 210px;
              height: 40px;
              font-size: 1rem;
              padding-left: 10px;
              border: 0;
              outline: none;
            }
            .sendCode {
              width: 80px;
              border: 0;
              outline: none;
              background-color: #fff;
              cursor: pointer;
            }
          }
          // 验证码样式的设置 end

          .checkinfo {
            font-size: 5px;
            margin-left: 18px;
          }
        }

        .el-form-item {
          margin-top: 24px;
          span {
            color: black;
            font-size: 16px;
            margin-right: 20px;
          }
          .el-input {
            width: 280px;
            height: 30px;
          }
        }
      }
      // 下一步按钮、完成按钮
      .el-button {
        position: absolute;
        top: 84%;
        left: 50%;
        transform: translate(-50%, -20%);
        width: 200px;
        border-radius: 50px;
      }
      .btn1{
        position: absolute;
        top: 84%;
        left: 38%;
        transform: translate(-50%, -20%);
        width: 200px;
        // height: 30px;
        border-radius: 50px;
        border: none;
        background-color: white;
      }
      .btn2{
        position: absolute;
        top: 84%;
        left: 64%;
        transform: translate(-50%, -20%);
        width: 200px;
        // height: 30px;
        border-radius: 50px;
        border: none;
        background-color: white;
      }
      // .el-button:first-of-type {
      //   position: absolute;
      //   top: 84%;
      //   left: 90%;
      //   transform: translate(-10%, -20%);
      //   width: 180px;
      //   border-radius: 50px;
      // }

      .personal-Finish{
        p{
          width: 100%;
          text-align: center;
          line-height: 100px;
          height: 100px;
          font-size: 32px;
          position: absolute;
          top: 48%;
          left: 50%;
          transform: translate(-50%, -20%);
        }
      }
    }
  }
}
</style>
