<template>
  <div id="login">
    <div class="login">
      <div class="left">
        <img src="../assets/favicon.png" alt />
        <h2>智学无忧后台管理系统</h2>
        <div>做最有态度、责任、良心的IT教育</div>
      </div>
      <div class="login-lin"></div>
      <div class="right">
        <el-form
          :model="ruleForm"
          status-icon
          :rules="rules"
          ref="ruleForm"
          label-width="100px"
          class="login-ruleForm"
        >
            <el-input type="string" v-model="ruleForm.userMobile" placeholder="请输入您的账号">
              
            </el-input>
            <span class="el-icon-s-custom"></span>
            <el-input
              type="password"
              v-model="ruleForm.userPassword"
              autocomplete="off"
              placeholder="请输入您的密码"
            ></el-input>
            <span class="el-icon-lock"></span>
          <el-form-item>
            <el-checkbox v-model="checked">是否记住密码</el-checkbox>
          </el-form-item>
            <el-button type="primary" @click="submitForm('ruleForm')" :loading="loading" style="width:100%;">登录</el-button>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
import Base from "@/util/Base64";
import Cookie from "@/util/Cookie";
export default {
  data() {
    return {
      checked: false, //记住密码状态
      loading: false, //提交时加载状态
      ruleForm: {
        userMobile: "",
        userPassword: ""
      },
      rules: {//登录验证
        userMobile: [
          { required: true, message: "请输入账号", trigger: "blur" }
        ],
        userPassword: [
          { required: true, message: "请输入密码", trigger: "blur" }
        ]
      }
    };
  },
  created() {
    if (Cookie.getCookie("userName")&&Cookie.getCookie("userPass")) {
      this.ruleForm.userMobile = Base.decode(Cookie.getCookie("userName"))
      this.ruleForm.userPassword = Base.decode(Cookie.getCookie("userPass"))
      this.checked = true
    }
  },
  methods: {
    submitForm(formName) {
      var _this = this;
      _this.$refs[formName].validate(valid => {
        if (valid&_this.ruleForm.userMobile!=""&_this.ruleForm.userPassword!="") {
          _this.loading = true; 
            _this.axios.get("/OAuth/authenticate",{
              params:{
                stuMobile: _this.ruleForm.userMobile,
                stuPassword: _this.ruleForm.userPassword
              }
            }).then(res => {
              if (_this.checked) {
                  Cookie.setCookie(
                    "userName", 
                    Base.encode(_this.ruleForm.userMobile), 
                    {maxAge: 60*60*24} 
                  );
                  Cookie.setCookie(
                    "userPass", 
                    Base.encode(_this.ruleForm.userPassword), 
                    {maxAge: 60*60*24} 
                  );
                }else{
                  Cookie.removeCookie("userName");
                  Cookie.removeCookie("userPass");
                }
              _this.loading = false;//加载状态
              var token = res.data.token_type + " " + res.data.access_token;
              sessionStorage.setItem("token",token);
              sessionStorage.setItem("userUid",res.data.profile.stuUid);
              sessionStorage.setItem("userName",res.data.profile.stuName);
              _this.$message({
                message: "登录成功",
                type: "success"
              });
              // _this.$router.replace("/home");
              _this.$router.replace({path:'/home'})
            }).catch(error => {
              setTimeout(function() {
                _this.loading = false;
              }, 2000);
              _this.$message({
                message: "账号或者密码错误",
                type: "error"
              });
            });
        } else {
          _this.$message({
            message: "请输入用户名和密码",
            type: "warning"
          });
          return false;
        }
      });
    },
  }
};
</script>

<style lang="scss" scoped>
@media screen and (max-width: 600px) {
  #login .login {
    font-size: 12px !important;
    .login-lin {
      display: none !important;
    }
    .left {
      float: none !important;
      width: 100% !important;
      display: none !important;
      margin: 0 !important;
    }
    .right {
      float: none !important;
      width: 100% !important;
      display: block !important;
      margin: 0 !important;
      padding: 20px 0px;
    }
  }
}

#login {
  height: 100%;
  display: flex;
  background: url(../assets/background.jpg) 0px 0px no-repeat;
  
  .login {
    margin: auto;
    border: 1px solid #ccc;
    background: #d8ecf5;
    border-radius: 8px;
    padding: 0px 25px;
    width: 560px;
    display: flex;
    .left {
      float: left;
      width: 250px;
      height: 250px;
      h2 {
        color: #0f998c;
        font-size: 23px;
      }
      div {
        color: #a9b4b6;
        font-size: 14px;
      }
      img {
        border-radius: 50%;
        width: 100px;
        box-shadow: 0px 0px 20px 7px #999;
        margin-top: 40px;
      }
    }
    .login-lin {
      margin: auto;
      height: 280px;
      width: 1px;
      background:linear-gradient(#fff,#999,#999,#fff);
    }
    .login-ruleForm{
      position: relative;
    }
    .right {
      margin-top: 35px;
      float: right;
      width: 250px;
      /* margin: auto; */
      .el-form-item {
        margin-bottom: 0;
        text-align: left;
        position: relative;
      }
      .el-form div {
        margin: 7px 0px;
        
      }
      .el-form div input {
        border-color: #6e9578;
      }
      .el-form span{
        position: absolute;
        left: 5px;
        margin-top: 13px;
        font-size: 26px;
      }
    }
  }
  /deep/ .el-form-item__content{
    margin: 0px !important;
  }
  /deep/ .el-input input{
    text-indent: 1.3em;
  }
}
</style>