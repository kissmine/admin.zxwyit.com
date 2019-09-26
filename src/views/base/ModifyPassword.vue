<template>
    <div id="form-Modifypassword">
        <el-card class="box-card">
            <div slot="header" class="clearfix ">
                <span class="el-icon-edit">修改密码</span>
            </div>
            <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
                <el-form-item label="旧密码" prop="formerPass">
                    <el-input type="password" v-model="ruleForm.formerPass" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="新密码" prop="pass">
                    <el-input type="password" v-model="ruleForm.pass" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="确认密码" prop="checkPass">
                    <el-input type="password" v-model="ruleForm.checkPass" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>
                    <el-button @click="resetForm('ruleForm')">重置</el-button>
                </el-form-item>
            </el-form>
        </el-card>
    </div>
</template>

<script>
export default {
     data() {
    // 旧密码
    // rule 返回一个object对象，在这里没有用到
    // value 返回一个输入框的值
    // callback 回调函数
    var former = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入旧密码'));
        } else {
          callback();
        }
      };
    //新密码
      var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入新密码'));
        } else {
          if (this.ruleForm.checkPass !== '') {
            this.$refs.ruleForm.validateField('checkPass');
          }
          callback();
        }
      };
      //确认密码
      var validatePass2 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入新密码'));
        } else if (value !== this.ruleForm.pass) {
          callback(new Error('两次输入密码不一致!'));
        } else {
          callback();
        }
      };
      return {
        ruleForm: {
            formerPass: '',     //旧密码
            pass: '',           //新密码
            checkPass: ''       //确认密码
        },
        rules: {
        //  旧密码
        // 表单验证规则
          formerPass: [
            { validator: former, trigger: 'blur' }
          ],
        //   新密码
          pass: [
            { validator: validatePass, trigger: 'blur' }
          ],
        //   确认密码
          checkPass: [
            { validator: validatePass2, trigger: 'blur' }
          ]
        }
      };
    },
    methods: {
      // 修改密码提交
      submitForm(formName) {
        var _this=this;
        _this.$refs[formName].validate((valid) => {
          if (valid) { 
              if(_this.ruleForm.pass.length>=6){
                // sessionStorage.userUid     用户唯一表示符
                // _this.ruleForm.formerPass  旧密码
                // _this.ruleForm.pass        新密码
                _this.axios.get("/User/ModifyPassword?uid="+sessionStorage.userUid+"&oldPassword="+_this.ruleForm.formerPass+"&newPassword="+_this.ruleForm.pass,
                ).then(function(res){
                  if(res.data.code=='1'){
                        _this.ruleForm.formerPass='';  //旧密码重新赋值为空
                        _this.ruleForm.pass='';        //新密码重新赋值为空
                        _this.ruleForm.checkPass='';   //确认密码重新赋值为空
                        _this.$message({
                            message: '修改成功',
                            type: 'success'
                        });
                        _this.$router.push({path:'/login'})    //修改成功后跳到登录页面
                        sessionStorage.removeItem("token")    //修改成功后清除会话存储的token（令牌）值
                    }else if(res.data.code=='-3'){
                        _this.$message.error('旧密码错误！');
                    }else if(res.data.code=='-2'){
                        _this.$message.error('旧密码不能为空！');
                    }else {
                        _this.$message.error('其它错误！'); 
                    }
                }).catch(function(req){
                    _this.$message.error('修改失败');
                })
            }else{
                _this.$message.error('密码不能少于6位！');
            }
          } else {
            return false;
          }
        });
      },
      // 重置
      resetForm(formName) {
        var _this=this;
        _this.$refs[formName].resetFields();
      }
    }
}
</script>

<style scoped>


</style>