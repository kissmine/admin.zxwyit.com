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
                width="400px"
                center
                >
                    <!-- 添加学生信息表单 -->
                    <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="80px" class="demo-ruleForm">
                        <!-- 选择班级 -->
                        <el-form-item label="班级" prop="classNum">
                            <el-select v-model="ruleForm.classNum" @change="Altere(ruleForm.classNum)" filterable placeholder="查找班级">
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
                            <el-form-item prop="dater">
                                <el-date-picker type="date" placeholder="选择日期" v-model="ruleForm.dater" style="width: 100%;"></el-date-picker>
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
                            <el-button v-show="increased" type="primary" @click="submitForm('ruleForm')">创建</el-button>
                            <el-button v-show="amend" type="primary" @click="AlterPe('ruleForm')">修改</el-button>
                            <el-button @click="resetForm('ruleForm')">取消</el-button>
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
                width="70">
                <template slot-scope="scope">
                    <span>{{scope.$index+1}}</span>
                </template>
                </el-table-column>
                <!-- 班级名称 -->
                <el-table-column
                label="班级名称">
                <template slot-scope="scope">
                    <span>{{scope.row.className}}</span>
                </template>
                </el-table-column>
                <!-- 学生信息 -->
                <el-table-column
                label="学生信息">
                <template slot-scope="scope">
                    <span>{{scope.row.stuName}}</span>
                </template>
                </el-table-column>
                <!-- 性别 -->
                <el-table-column
                label="性别">
                <template slot-scope="scope">
                    <span>{{scope.row.stuSex}}</span>
                </template>
                </el-table-column>
                <!-- 手机号 -->
                <el-table-column
                label="手机号">
                <template slot-scope="scope">
                    <span>{{scope.row.stuMobile}}</span>
                </template>
                </el-table-column>
                <!-- 出生日期 -->
                <el-table-column
                label="出生日期">
                <template slot-scope="scope">
                    <span>{{scope.row.stuBirthDay}}</span>
                </template>
                </el-table-column>
                <!-- 年龄 -->
                <el-table-column
                label="年龄">
                <template slot-scope="scope">
                    <span>{{scope.row.stuAge}}</span>
                </template>
                </el-table-column>
                <!-- 操作 -->
                <el-table-column width="160"  label="操作">
                <template slot-scope="scope">
                    <el-button
                    size="mini"
                    @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
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
        amend:false,
        increased:true,
        //学生唯一标识符
        stuUid:'',
        Classval:'',
        Index:"",
        // 添加表单验证数据
        ruleForm: {
            name: '',//用户的绑定数据
            classNum: '',//班级编号的绑定数据
            dater: '',//生日的绑定数据
            phone:'',//手机号的绑定数据
            radio:'男',//性别的绑定数据
            passwo:''//密码的绑定数据
        },
        rules: {
          name: [//用户的判断
            { required: true, message: '请输入学生名字', trigger: 'blur' }
            // { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
          classNum: [//班级的判断
            { required: true, message: '请选择班级', trigger: 'change' }
          ],
          dater: [//生日日期的判断
            { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
          ],
          phone: [//手机号的判断
            { required: true, message: '请输入手机号', trigger: 'blur' },
            { min: 11, max: 11, message: '手机号必须是11位', trigger: 'blur' }
          ],
          passwo: [//密码的判断
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min: 6, max: 111, message: '密码必须大于6位', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
        //编辑
        handleEdit(index, row) {
            // console.log(row.stuBirthDay = new Date(row.stuBirthDay))
            var _this = this
            _this.Index = index
            _this.amend = true,
            _this.increased = false,
            _this.centerDialogVisible = true
            _this.popUptitle = "修改学生信息"
            _this.ruleForm = {
                name:row.stuName,
                classNum:row.className,
                dater:new Date(row.stuBirthDay),
                phone:row.stuMobile,
                radio:row.stuSex,
                passwo:row.stuPassword
            }
            _this.stuUid = row.stuUid
        },
        //删除
        handleDelete(index, row){
            var _this = this
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
                        _this.tableData.splice(index,1)
                        // _this.Refresh(row.classId)
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
            _this.Classval = val
            _this.Refresh(val);
        },
        Altere(val){
            var _this = this
            _this.Classval = val
            
        },
        // 增加学生
        increase(){
            var _this = this
            _this.centerDialogVisible = true
            _this.popUptitle = "新增学生信息"
            _this.amend = false
            _this.increased = true
            for(let k in _this.ruleForm){
                _this.ruleForm[k] = ""
            }
        },
        submitForm(formName){
            var _this = this
            _this.$refs[formName].validate((valid) => {
            if (valid) {
                _this.axios.post("Student/AddStudent",
                    {
                        "stuName":_this.ruleForm.name,//学生姓名
                        "stuClassId":_this.ruleForm.classNum,//班级编号
                        "stuBirthDay":_this.ruleForm.dater,//生日
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
                        // if(_this.value==_this.ruleForm.classNum){
                            
                        // }else{
                        //     console.log(789456)
                        // }
                        _this.Refresh(_this.ruleForm.classNum);
                        _this.value = _this.ruleForm.classNum
                    }else if(res.data.code==0){
                            _this.$message({
                                 showClose: true,
                                 message: '新增失败',
                                 type: 'error'
                            });
                    }else{
                        _this.$message({
                            showClose: true,
                            message: '新增失败,不能新增',
                            type: 'error'
                        });
                    }
                })
            } else {
                return false;
            }
            });
        },
        //取消
        resetForm(formName,event){
            var _this = this
            _this.centerDialogVisible = false
            _this.$refs[formName].resetFields();
        },
        //修改
        AlterPe(formName){
            var _this = this
            var wenDatenun = new Date(_this.ruleForm.dater)
            var Datenum = new Date()
            var peryear = Datenum.getFullYear()
            var befyear = wenDatenun.getFullYear()
            _this.$refs[formName].validate((valid)=>{
                if(valid){
                    _this.$confirm('此操作将修改学生信息, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                    }).then(() => {
                        _this.axios.post("Student/ModifyStudent",
                                {
                                    "stuUid":_this.stuUid,// 要修改学生的唯一标识符
                                    "stuName":_this.ruleForm.name,//要修改的名称
                                    "stuBirthDay":new Date(_this.ruleForm.dater),//要修改的生日
                                    "stuClassId":_this.Classval,//班级编号
                                    "stuMobile":_this.ruleForm.phone,//要修改的手机号
                                    "stuPassword":_this.ruleForm.passwo,//要修改的密码
                                    "stuSex":_this.ruleForm.radio,//要修改的性别
                                }
                        ).then(function(res){
                                if(res.data.code==1){
                                    _this.$message({
                                        showClose:true,
                                        type: 'success',
                                        message: '修改成功!'
                                    });
                                    if(_this.value==_this.Classval){
                                        var num = 0
                                        var numthe = 0
                                        var d = _this.ruleForm.dater
                                        if(d.getMonth() + 1>9){
                                            numthe = ""
                                        }else{
                                            numthe = 0
                                        }
                                        if(d.getDate()>9){
                                            num = ""
                                        }else{
                                            num = 0
                                        }
                                        var datetime=d.getFullYear() + '-'+numthe+'' + (d.getMonth() + 1) + '-'+num+'' + d.getDate() + 'T0' + d.getHours() + ':0' + d.getMinutes() + ':0' + d.getSeconds();
                                        _this.tableData[_this.Index].stuName = _this.ruleForm.name
                                        _this.tableData[_this.Index].stuBirthDay = datetime
                                        _this.tableData[_this.Index].stuClassId = _this.Classval
                                        _this.tableData[_this.Index].stuMobile = _this.ruleForm.phone
                                        _this.tableData[_this.Index].stuPassword = _this.ruleForm.passwo
                                        _this.tableData[_this.Index].stuSex = _this.ruleForm.radio
                                        _this.tableData[_this.Index].stuAge = Math.floor(peryear-befyear)
                                    }else{
                                        _this.tableData.splice(_this.Index,1)
                                    }
                                    // _this.Refresh(_this.Classval);
                                    // _this.value = _this.Classval
                                }else if(res.data.code==0){
                                    _this.$message({
                                        showClose: true,
                                        message: '修改失败',
                                        type: 'error'
                                    });
                                }else{
                                    _this.$message({
                                        showClose: true,
                                        message: '修改失败,不能修改',
                                        type: 'error'
                                    });
                                }
                        })
                    }).catch(() => {
                        this.$message({
                            type: 'info',
                            message: '已取消修改'
                        });          
                    });
                        
                }else{
                    return false;
                }
            })
        },
        // 刷新数据
        Refresh(val){
            var _this = this
            _this.axios.get('Student/GetClassStudent?classId='+val).then(function(res){
                    _this.tableData = res.data
            })
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
        /deep/ .el-dialog__wrapper{
            padding-top:70px;
        }
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
            margin-top: 20px !important;
            .el-input__inner{
                width: 100%;
                height: 40px;
            }
        }
        /deep/ .el-table__row td{
            width:19%;
            span{
                overflow:hidden;
                text-overflow:ellipsis;
                white-space:nowrap;
            }
        }
        
    }
</style>