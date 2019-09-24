<template>
<!-- 学生管理 -->
  <div id="StudentMag" >
    <el-card class="box-card">
        <div slot="header" class="clearfix">
            <!-- 搜索部分 -->
            <el-select v-model="value" @change="Alter(value)" filterable placeholder="查找班级">
                <el-option
                v-for="item in options"
                :key="item.classId"
                :label="item.className"
                :value="item.classId"
                >
                </el-option>
            </el-select>
            <!-- 搜索部分结束 -->
            <!-- 添加学生部分 -->
            <div class="student-Add" @click="increase" >
                <span class="el-icon-circle-plus-outline"></span>
                <i>新增学生</i>
            </div>
            <!-- 添加学生部分结束 -->
            <!-- 添加学生的弹出框 -->
                <el-dialog
                :title="popUptitle"
                :visible.sync="centerDialogVisible"
                width="25%"
                center>
                    <!-- 添加学生信息表单 -->
                    <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="80px" class="demo-ruleForm">
                        <!-- 选择班级 -->
                        <el-form-item label="班级" prop="classNum">
                            <el-select v-model="ruleForm.classNum" filterable placeholder="查找班级">
                                <el-option
                                v-for="item in options"
                                :key="item.classId"
                                :label="item.className"
                                :value="item.classId"
                                >
                                </el-option>
                            </el-select>
                        </el-form-item>
                        <!-- 填写学生姓名 -->
                        <el-form-item label="学生姓名" prop="name">
                            <el-input v-model="ruleForm.name" placeholder="填写学生姓名" ></el-input>
                        </el-form-item>
                        <!-- 选择生日 -->
                        <el-form-item label="生日" required>
                            <el-col :span="11">
                            <el-form-item prop="date">
                                <el-date-picker type="date" placeholder="选择日期" v-model="ruleForm.date" style="width: 100%;"></el-date-picker>
                            </el-form-item>
                            </el-col>
                            <el-col class="line" :span="2"></el-col>
                        </el-form-item>
                        <!-- 填写手机号 -->
                        <el-form-item label="手机号" prop="phone">
                            <el-input v-model="ruleForm.phone" placeholder="填写学生手机号" ></el-input>
                        </el-form-item>
                        <!-- 选择性别 -->
                        <el-form-item label="性别">
                            <el-radio v-model="ruleForm.radio" label="男">男</el-radio>
                            <el-radio v-model="ruleForm.radio" label="女">女</el-radio>
                        </el-form-item>
                        <!-- 填写密码 -->
                        <el-form-item label="密码" prop="passwo">
                            <el-input v-model="ruleForm.passwo" placeholder="填写学生密码" ></el-input>
                        </el-form-item>
                        <el-form-item>
                            <el-button type="primary" @click="submitForm('ruleForm')">创建</el-button>
                            <el-button @click="resetForm()">取消</el-button>
                        </el-form-item>
                    </el-form>
                    <!-- 添加学生信息表单结束 -->
                </el-dialog>
            <!-- 添加学生的弹出框 -->
        </div>
        <!-- 班级学生信息 -->
            <el-table
                :data="tableData"
                style="width: 100%">
                <!-- 下标 -->
                <el-table-column
                label="#"
                width="100">
                <template slot-scope="scope">
                    <span>{{scope.$index+1}}</span>
                </template>
                </el-table-column>
                <!-- 班级名称 -->
                <el-table-column
                label="班级名称"
                width="180">
                <template slot-scope="scope">
                    <span>{{scope.row.className}}</span>
                </template>
                </el-table-column>
                <!-- 学生信息 -->
                <el-table-column
                label="学生信息"
                width="180">
                <template slot-scope="scope">
                    <span>{{scope.row.stuName}}</span>
                </template>
                </el-table-column>
                <!-- 性别 -->
                <el-table-column
                label="性别"
                width="180">
                <template slot-scope="scope">
                    <span>{{scope.row.stuSex}}</span>
                </template>
                </el-table-column>
                <!-- 手机号 -->
                <el-table-column
                label="手机号"
                width="180">
                <template slot-scope="scope">
                    <span>{{scope.row.stuMobile}}</span>
                </template>
                </el-table-column>
                <!-- 出生日期 -->
                <el-table-column
                label="出生日期"
                width="180">
                <template slot-scope="scope">
                    <span>{{scope.row.stuBirthDay}}</span>
                </template>
                </el-table-column>
                <!-- 年龄 -->
                <el-table-column
                label="年龄"
                width="180">
                <template slot-scope="scope">
                    <span>{{scope.row.stuAge}}</span>
                </template>
                </el-table-column>
                <!-- 操作 -->
                <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button
                    size="mini"
                    @click="handleEdit(scope.$scope, scope.row)">编辑</el-button>
                    <el-button
                    size="mini"
                    type="danger"
                    @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
                </el-table-column>
            </el-table>
        <!-- 班级学生信息结束 -->
    </el-card>
  </div>
</template>

<script>
export default {
    data() {
      return {
        //搜索框内容
        options: [],
        value:'',
        //学生信息
        tableData: [],
        // 添加弹出框的判断方法
        centerDialogVisible:false,
        //弹出框主体
        popUptitle:'新增学生信息',
        // 添加表单验证数据
        ruleForm: {
            name: '',
            classNum: '',
            date: '',
            phone:'',
            radio:'男',
            passwo:''
        },
        rules: {
          name: [
            { required: true, message: '请输入学生名字', trigger: 'blur' }
            // { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
          classNum: [
            { required: true, message: '请选择班级', trigger: 'change' }
          ],
          date: [
            { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
          ],
          phone: [
            { required: true, message: '请输入手机号', trigger: 'blur' },
            { min: 11, max: 11, message: '手机号必须是11位', trigger: 'blur' }
          ],
          passwo: [
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min: 6, max: 111, message: '密码必须大于6位', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
        //编辑
        handleEdit(index, row) {
            console.log(index, row);
            var _this = this
            _this.centerDialogVisible = true
            _this.popUptitle = "修改学生信息"
            // _this.name = row.stuName
            // _this.classNum = row.className
            // _this.date = row.stuBirthDay
            // _this.phone = row.stuMobile
            // _this.radio = row.stuSex
            // _this.passwo = row.stuPassword
        },
        //删除
        handleDelete(index, row) {
            var _this = this
            // console.log(index, row);
            // this.tableData.splice(index,1)
            _this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
                }).then(() => {
                _this.axios.get("Student/RemoveStudent?uid="+row.stuUid).then(function(res){
                    if(res.data.code==1){
                        _this.$message({
                            showClose:true,
                            type: 'success',
                            message: '删除成功!'
                        });
                        _this.axios.get('Student/GetClassStudent?classId='+row.classId).then(function(res){
                            _this.tableData = res.data
                        })
                    }else{
                        _this.$message({
                            showClose: true,
                            message: '删除错误,不能删除',
                            type: 'error'
                        });
                    }
                })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });          
                });
        },
        /**
         * 查找框事件
         */
        Alter(val){
            var _this = this
            _this.axios.get('Student/GetClassStudent?classId='+val).then(function(res){
                _this.tableData = res.data
            })
        },
        // 增加学生
        increase(){
            var _this = this
            _this.centerDialogVisible = true
            _this.popUptitle = "新增学生信息"
        },
        submitForm(formName) {
            var _this = this
            _this.$refs[formName].validate((valid) => {
            if (valid) {
                _this.axios.post("Student/AddStudent",
                    {
                        "stuName":_this.ruleForm.name,//学生姓名
                        "stuClassId":_this.ruleForm.classNum,//班级编号
                        "stuBirthDay":_this.ruleForm.date,//生日
                        "stuMobile":_this.ruleForm.phone,//手机号
                        "stuPassword":_this.ruleForm.passwo,//登录密码,
                        "stuSex":_this.ruleForm.radio//性别
                    }
                ).then(function(res){
                    if(res.data.code==1){
                        _this.$message({
                            showClose:true,
                            type: 'success',
                            message: '新增成功!'
                        });
                        _this.axios.get('Student/GetClassStudent?classId='+_this.ruleForm.classNum).then(function(res){
                            _this.tableData = res.data
                        })
                    }else{
                        _this.$message({
                            showClose: true,
                            message: '新增失败',
                            type: 'error'
                        });
                    }
                })
            } else {
                
                return false;
            }
            });
        },
        resetForm() {
            var _this = this
            _this.centerDialogVisible = false
            for(let k in _this.ruleForm){
                _this.ruleForm[k] = ""
            }
        }
    },
    //创建后
    created(){
        var _this = this
        _this.axios.get("Class/GetAllClass").then(function(res){
            _this.options = res.data
        })
    }
}
</script>

<style lang="less" scoped>
    #StudentMag{
        border: 1px solid #ccc;
        box-shadow: 0px 0px 10px 3px #ececec;
        border-radius: 5px;
        //搜索部分
        /deep/ .el-input__inner{
            height: 30px;
            width: 180px;
            font-size: 12px;
        }
        /deep/ .el-select__caret.el-input__icon.el-icon-arrow-up{
            line-height: 30px;
            font-size: 12px;
        }
        // 添加学生
        .student-Add{
            cursor: pointer;
            height: 100%;
            display: inline-block;
            color: rgb(8, 175, 252);
            margin-left: 10px;

            span{
                margin-right: 3px;
            }
            i{  
                font-size: 14px;
                font-style: normal;
                font-weight: 600;
            }
        }
        /deep/ .el-dialog.el-dialog--center{
            .el-input__inner{
                width: 100%;
                height: 40px;
            }
        }
         
    }
</style>