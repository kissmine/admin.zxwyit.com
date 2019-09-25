<template>
    <div>
        <!-- <h1 class="el-icon-edit">修改密码</h1> -->
        <el-card class="box-card">
            <div slot="header" class="clearfix ">
                <span class="el-icon-edit">修改密码</span>
            </div>
            <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
                <el-form-item label="旧密码" prop="formerpass">
                    <el-input type="password" v-model="ruleForm.formerpass" autocomplete="off"></el-input>
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
    var former = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else {
          callback();
        }
      };
    //新密码
      var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
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
          callback(new Error('请再次输入密码'));
        } else if (value !== this.ruleForm.pass) {
          callback(new Error('两次输入密码不一致!'));
        } else {
          callback();
        }
      };
      return {
        ruleForm: {
            formerpass: '',     //旧密码
            pass: '',           //新密码
            checkPass: ''       //确认密码
        },
        rules: {
        //  旧密码
          formerpass: [
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
          var that=this
        this.$refs[formName].validate((valid) => {
          if (valid) { 
              if(this.ruleForm.pass.length>=6){
                this.axios.get("/User/ModifyPassword?uid="+sessionStorage.userUid+"&oldPassword="+this.ruleForm.formerpass+"&newPassword="+this.ruleForm.pass,
                ).then(function(res){
                  if(res.data.code=='1'){
                        that.ruleForm.formerpass='';  //旧密码重新赋值为空
                        that.ruleForm.pass='';        //新密码重新赋值为空
                        that.ruleForm.checkPass='';   //确认密码重新赋值为空
                        that.$message({
                            message: '修改成功',
                            type: 'success'
                        });
                        
                  }else if(res.data.code=='-3'){
                        that.$message.error('旧密码错误！');
                    }else if(res.data.code=='-2'){
                            that.$message.error('旧密码不能为空！');
                    }else {
                        that.$message.error('其它错误！'); 
                    }
                }).catch(function(req){
                    that.$message.error('修改失败');
                })
            }else{
                that.$message.error('密码不能少于6位！');
            }
          } else {
            return false;
          }
        });
      },
      resetForm(formName) {
        this.$refs[formName].resetFields();
      }
    }
}
</script>

<style scoped>


</style>