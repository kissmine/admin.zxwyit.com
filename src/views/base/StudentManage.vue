<template>
<!-- 学生管理 -->
  <div id="StudentMag" >
    <el-card class="box-card">
        <div slot="header" class="clearfix">
            <!-- 搜索部分 -->
            <el-select v-model="value" @change="findClass(value)" filterable placeholder="查找班级">
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
            <div class="student-Add" @click="additionStudent" >
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
                            <el-select v-model="ruleForm.classNum" @change="findClassr(ruleForm.classNum)" filterable placeholder="查找班级">
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
                            <el-input type="password" v-model="ruleForm.passwo" placeholder="填写学生密码" ></el-input>
                        </el-form-item>
                        <el-form-item>
                            <el-button v-show="increased" type="primary" @click="foundStudent('ruleForm')">创建</el-button>
                            <el-button v-show="amend" type="primary" @click="amendStudent('ruleForm')">修改</el-button>
                            <el-button @click="cancel('ruleForm')">取消</el-button>
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
                    @click="compileStudent(scope.$index, scope.row)">编辑</el-button>
                    <el-button
                    size="mini"
                    type="danger"
                    @click="deleteStudent(scope.$index, scope.row)">删除</el-button>
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
        classVal:'',
        indexVal:"",
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
         /**
         * 修改学生的方法
         */
        compileStudent(index, row) {
            var _this = this //使用变量接收this减少this的查找优化代码
            _this.indexVal = index//获取当前下标
            _this.amend = true,//验证修改显示的判断数据
            _this.increased = false,//验证增加显示的判断数据
            _this.centerDialogVisible = true//显示对话框
            _this.popUptitle = "修改学生信息"//改变对话框的标题信息
            _this.ruleForm = {//吧当行的数据放在当前的对话框里表单对应的数据
                name:row.stuName,//学生姓名
                classNum:row.className,//班级名称
                dater:new Date(row.stuBirthDay),//学生生日
                phone:row.stuMobile,//学生手机号
                radio:row.stuSex,//学生性别
                passwo:row.stuPassword//学生密码
            }
            _this.stuUid = row.stuUid//当前的学生唯一标示用户来进行修改的判断
        },
        /**
         * 删除学生的方法
         */
        deleteStudent(index, row){
            var _this = this
            _this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {//提示用户是否删除
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
                }).then(() => {
                _this.axios.get("Student/RemoveStudent?uid="+row.stuUid).then(function(res){//调用删除接口
                    if(res.data.code==1){
                        _this.$message({
                            showClose:true,
                            type: 'success',
                            message: '删除成功!'
                        });
                        _this.tableData.splice(index,1)//删除成功就删除指定下标的数据
                        // _this.Refresh(row.classId)
                    }else{
                        _this.$message({//提示该用户不能删除
                            showClose: true,
                            message: '删除错误,不能删除',
                            type: 'error'
                        });
                    }
                })
                }).catch(() => {//提示该用户取消了删除
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });          
                });
        },
        /**
         *查找班级的方法
         */
        findClass(val){
            var _this = this
            _this.classVal = val//将当前的班级id给classVal数据
            _this.acquireClass(val);//将当前的班级id传给acquireClass方法
        },
        /**
         * 在对话框查找班级的方法
         */
        findClassr(val){
            var _this = this
            _this.classVal = val//将当前的班级id给classVal数据
        },
        /**
         * 取消新增和修改的方法
         */
        cancel(formName){
            var _this = this
            _this.centerDialogVisible = false//点击取消将对话框隐藏
            _this.$refs[formName].resetFields();//将表单的数据清空
        },
        /**
         * 新增学生的方法
         */
        additionStudent(){
            var _this = this
            _this.centerDialogVisible = true//点击新增时显示对话框
            _this.popUptitle = "新增学生信息"//点击改变对话框的标题
            _this.amend = false//修改按钮隐藏
            _this.increased = true//新增按钮显示
            for(let k in _this.ruleForm){//进来时将数据清空一次
                _this.ruleForm[k] = ""
            }
            _this.ruleForm.radio = "男"//默认显示性别的数据
        },
        foundStudent(formName){
            var _this = this
            _this.$refs[formName].validate((valid) => {//判断表单的值是否正确、正确才能进入添加数据
            if (valid) {
                _this.axios.post("Student/AddStudent",//调用新增的接口
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
                        _this.acquireClass(_this.ruleForm.classNum);//新增成功后获取个班级的数据
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
        /**
         * 修改的方法
         */
        amendStudent(formName){
            var _this = this
            var wenDatenun = new Date(_this.ruleForm.dater)//获取你修改的时间
            var Datenum = new Date()//获取当前时间
            var peryear = Datenum.getFullYear()//将当前时间只获取年份
            var befyear = wenDatenun.getFullYear()//获取你修改的时间只获取年份
            _this.$refs[formName].validate((valid)=>{//判断表单的值是否正确、正确才能进入修改学生数据
                if(valid){
                    _this.$confirm('此操作将修改学生信息, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                    }).then(() => {
                        _this.axios.post("Student/ModifyStudent",//调用修改的接口
                                {
                                    "stuUid":_this.stuUid,// 要修改学生的唯一标识符
                                    "stuName":_this.ruleForm.name,//要修改的名称
                                    "stuBirthDay":new Date(_this.ruleForm.dater),//要修改的生日
                                    "stuClassId":_this.classVal,//班级编号
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
                                    if(_this.value==_this.classVal){//判断你修改的班级的Id是否跟我的当前页面的班级的Id相同
                                        var num = 0
                                        var numthe = 0
                                        var d = _this.ruleForm.dater
                                        if(d.getMonth() + 1>9){//判断修改当前时间的月份如果大于9就不显示0小于9就显示0
                                            numthe = ""
                                        }else{
                                            numthe = 0
                                        }
                                        if(d.getDate()>9){//判断修改当前时间的日期如果大于9就不显示0小于9就显示0
                                            num = ""
                                        }else{
                                            num = 0
                                        }
                                        // 将修改的数据显示在当前得班级上
                                        var datetime=d.getFullYear() + '-'+numthe+'' + (d.getMonth() + 1) + '-'+num+'' + d.getDate() + 'T0' + d.getHours() + ':0' + d.getMinutes() + ':0' + d.getSeconds();
                                        _this.tableData[_this.indexVal].stuName = _this.ruleForm.name
                                        _this.tableData[_this.indexVal].stuBirthDay = datetime
                                        _this.tableData[_this.indexVal].stuClassId = _this.classVal
                                        _this.tableData[_this.indexVal].stuMobile = _this.ruleForm.phone
                                        _this.tableData[_this.indexVal].stuPassword = _this.ruleForm.passwo
                                        _this.tableData[_this.indexVal].stuSex = _this.ruleForm.radio
                                        _this.tableData[_this.indexVal].stuAge = Math.floor(peryear-befyear)
                                    }else{//当前班级的Id不同就删除修改的当行数据
                                        _this.tableData.splice(_this.indexVal,1)
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
        /**
         * 获取的改变的班级数据的方法
         */
        acquireClass(val){
            var _this = this
            _this.axios.get('Student/GetClassStudent?classId='+val).then(function(res){
                    _this.tableData = res.data
            })
        }
    },
    //创建后
    created(){
        var _this = this
        // 获取查找班级数据的接口
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