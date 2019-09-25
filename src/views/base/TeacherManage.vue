<template>
  <!-- 用户管理界面 -->
  <div id="userManage">
    <!-- 单选切换导航栏 -->
    <div id="nav">
      <el-radio-group v-model="radio2">
        <el-button
          type="text"
          class="el-icon-circle-plus-outline"
          @click="dialogFormVisible = true"
          style="padding:10px;"
        >新增用户</el-button>
        <el-radio :label="0">全部</el-radio>
        <el-radio :label="index+1" v-for="(item,index) in userName" :key="index" >{{item.userTypeTypeName}}</el-radio>
      </el-radio-group>
    </div>
    <!-- 表格 -->
    <el-table :data="tableData" style="width: 100%">
      <!-- 编号 -->
      <el-table-column width="50" label="#" type="index" :index="indexMethod"></el-table-column>
      <!-- 用户名称 -->
      <el-table-column label="用户名称" width="150" style="padding-left:20px;">
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{scope.row.userName}}</span>
        </template>
      </el-table-column>
      <!-- 手机号 -->
      <el-table-column label="手机号" width="120">
        <template slot-scope="scope">
          <span>{{scope.row.userMobile}}</span>
        </template>
      </el-table-column>
      <!-- 密码 -->
      <el-table-column label="密码" width="120" style="padding-left:20px;">
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{scope.row.userPassword}}</span>
        </template>
      </el-table-column>
      <!-- 性别 -->
      <el-table-column label="性别" width="100" style="padding-left:20px;">
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{scope.row.userSex}}</span>
        </template>
      </el-table-column>
      <!-- 角色名称 -->
      <el-table-column label="角色名称" width="100" style="padding-left:20px;">
        <template slot-scope="scope">
          <span style="margin-left: 10px">{{scope.row.userTypeTypeName}}</span>
        </template>
      </el-table-column>
      <!-- 操作 -->
      <el-table-column label="操作">
        <template slot-scope="scope">
          <el-button size="mini" @click="updelet(scope.row)">编辑</el-button>
          <!-- 按钮组件属性判断是否禁用 -->
          <el-button size="mini" type="danger"
         :disabled="scope.row.disableDelete"  
          @click="handleDelete(scope.$index, scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 修改用户信息 -->
    <el-dialog title="修改用户信息" :visible.sync="handleEdit" width="43%">
      <el-form>
        <el-form-item label="用户名称" :label-width="formLabelWidth">
          <el-input v-model="polist.userName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="手机号" :label-width="formLabelWidth">
          <el-input v-model="polist.userMobile" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" :label-width="formLabelWidth">
          <el-input v-model="polist.userPassword" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="性别" :label-width="formLabelWidth">
          <el-radio-group v-model="radio">
            <el-radio :label="1">男</el-radio>
            <el-radio :label="2">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="角色" :label-width="formLabelWidth">
          <el-select v-model="value" @change="updaterty(value)">
            <el-option :label="item.userTypeTypeName" :value="item.userTypeId" v-for="(item,index) in userName" :key="index"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="handleEdit=false">取 消</el-button>
        <el-button type="primary" @click="updata(polist)">修改</el-button>
      </div>
    </el-dialog>
    <!-- 新增用户弹框 -->
    <el-dialog title="新增用户信息" :visible.sync="dialogFormVisible" width="43%">
      <el-form :model="form"
       :rules="rules"
       ref="ruleForm"
       class="demo-ruleForm"
       >
        <el-form-item label="用户名称" :label-width="formLabelWidth" prop="userName"> 
          <el-input v-model="form.userName" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="手机号"
         :label-width="formLabelWidth"
          prop="userMobile">
          <el-input v-model="form.userMobile" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" :label-width="formLabelWidth" prop="userPassword">
          <el-input v-model="form.userPassword" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="性别" :label-width="formLabelWidth" >
          <el-radio-group v-model="radio">
            <el-radio :label="1">男</el-radio>
            <el-radio :label="2">女</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="角色" :label-width="formLabelWidth" prop="region">
          <el-select v-model="form.userTypeTypeName" placeholder="请选择">
            <el-option :label="item.userTypeTypeName" :value="item.userTypeId" v-for="(item,index) in userName" :key="index"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer" >
        <el-button @click="dialogFormVisible=false">取 消</el-button>
        <el-button type="primary" @click="add()">添加</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 新增弹框验证
      ruleForm:{
          userName:'',//用户名验证
          userMobile:'',//手机验证
          userPassword:'',//密码验证
          region: '',//角色选择验证
      },
      rules:{//规则验证新增弹框输入表达式
        userName:[
            { required: true, message: '请输入用户名称', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
        ],
        userMobile:[
            { required: true, message: '请输入手机号', trigger: 'blur' },
            { min:11, max:11, message: '长度为11个数字', trigger: 'blur' }  

        ],
        userPassword:[
            { required: true, message: '请输入密码', trigger: 'blur' },
            { min:6, message: '长度最少是 6 位数字', trigger: 'blur' }
        ],
      },

      radio:1, //定义男女性别变量默认选中女状态
      radio2:0, //过滤器默认选中全部状态
      tableData: [], //接口请求的数据
      dialogFormVisible: false, //定义新增用户信息变量
      handleEdit: false, //修改用户信息弹框变量
      polist:[], //定义修改弹框里的数据
      tableData2:[],//定义变量用来刷选所需的请求数据
      form: {
        userName: "", //用户名称
        userMobile: "", //用户手机
        userPassword: "", //用户密码
        userSex: "", //用户性别
        userTypeTypeName: "", //用户角色
      },
      formLabelWidth: "120px",//单元格间隔宽度
      userUserId: "",
      value: "", //当前默认的Id
      valueone: "", //当前选中的id
      userName:[]//获取所有角色
    };
  },
  watch:{//监听筛选数据
    radio2(Number){
          var that=this;
        if(Number==0){
          that.tableData=that.tableData2
        }
        for (var i = 1; i <=that.userName.length; i++) {//循环角色
            if(Number==i){
              that.tableData=that.tableData2.filter((item,index)=>{
              return item.userTypeTypeName==that.userName[i-1].userTypeTypeName
          })
        }   
      }
    }
  },
  created() {
    var that=this;
    that.data()//调用重新请求
    that.axios.get("/UserType/GetUserRoles",{}).then(res=>{//获取所有角色名称信息
      that.userName=res.data
    })
  },
  //方法
  // inject: ["reload"], //调用app里的路由方法
  methods: {
    data() {//所用用户信息
        var _this = this;
      _this.axios.get("/User/GetTeachers", {}).then(res => {
        _this.tableData  = res.data;
        _this.tableData2 = res.data;
      });
      },
     
    // 抒写用户编号
    indexMethod(index) {
      return index + 1;
    },
    //点击弹出修改弹框
    updelet(item) {
      this.handleEdit = true; //修改弹框显示
      this.valueone = item.userUserTypeId; //给当前选中的userUserTypeId赋值
      this.polist = item; //给当前选中编辑的对象赋值
      this.radio = item.userSex == "男" ? 1 : 2; //当按钮默认值为男时赋值为1
      this.value = item.userTypeTypeName; //给当前的默认的角色赋值
    },
    //新增用户信息
    add() {
      var th = this;
      th.form.userSex = th.radio == 1 ? "男" : "女";//获取当前性别所选值
      var userUserTypeId = Number(th.form.userTypeTypeName); //获取当前角色名称所对应数字并转型
      //判断当前新增弹框里是否有未填完 未填完则不执行添加操作
      if(th.form.userName&&th.form.userMobile&&th.form.userPassword&&th.form.userTypeTypeName!==""){                        
      th.axios.post("/User/AddTeacher", {
          userName: th.form.userName,
          userMobile: th.form.userMobile,
          userPassword: th.form.userPassword,
          userSex: th.form.userSex,
          userUserTypeId: userUserTypeId
        })
        .then(res => {        
          if (res.data.code == 1) {
            th.$message({
              type: "success",
              message: "添加成功!"
            });
             th.data()//调用重新加载数据
             th.radio2=0;//给当前按钮状态切换到0全部状态
          }
          if (res.data.code == -2) {
            th.$message({
              type: "info",
              message: "参数错误!"
            });
          }
          if (res.data.code == 0) {
            th.$message({
              type: "info",
              message: "添加失败!数据未变动"
            });
          }
          if (res.data.code == -1) {
            th.$message({
              type: "error",
              message: "系统错误!"
              });
            }
         
        });
      th.dialogFormVisible = false; //当新增用户时间完成后关闭弹框赋值为flase
      }else{
              th.$message({
              type: "error",
              message: "信息未填写完！添加失败"
              });
            }
      
    },
    //修改用户信息
    updata(polist) {
      var that = this;
      var userSex = that.radio == 1 ? "男" : "女"; //取值默认按钮值
      that.axios
        .post("User/ModifyTeacher", {
          userUid: polist.userUid,
          userName: polist.userName,
          userMobile: polist.userMobile,
          userSex: userSex,
          userUserTypeId: that.valueone,
          userPassword: polist.userPassword
        })
        .then((res)=> {
          console.log(res.data);
          if (res.data.code == 1) {
             that.data()
             that.radio2=0
             that.data()
            that.$message({
              type: "success",
              message: "修改成功!"
            });
          }
          if (res.data.code == -2) {
            that.$message({
              type: "info",
              message: "参数错误!"
            });
          }
          if (res.data.code == 0) {
            that.$message({
              type: "info",
              message: "修改失败!"
            });
          }
          if (res.data.code == -1) {
            that.$message({
              type: "error",
              message: "系统错误!"
          });
        }
      });
      that.handleEdit = false;
    },
    //删除用户
    handleDelete(index, row) {
      var that = this;
      that.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        })
        .then(() => {
          // 点击确认后请求数据删除
          that.axios.post("User/RemoveTeacher?uid="+row.userUid)
            .then(res => {
              if(res.data.code==1){
               that.data()
               that.radio2=0
                that.$message({
                type: "success",
                message: "删除成功"
              });
              }
               if(res.data.code==0){
                  that.$message({
                  type: "info",
                  message: "删除失败"
              });
               } 
               if(res.data.code==-2){
                  that.$message({
                  type: "info",
                  message: "删除失败！参数错误"
                  });
               }if(res.data.code==-1){
                  that.$message({
                  type: "error",
                  message: "系统错误"
                  });
               }
            });
        })
        .catch(() => {
          //否则取消并
          that.$message({
            type: "info", 
            message: "已取消删除"
          });
        });
    },
    //当前选中的改变userUserTypeId事件
    updaterty(val) {
      this.valueone = val; //当选中的值发生改变后重新给变量赋值
    }
  }
};
</script>

<style lang="scss" scoped>
#userManage {
  width: 98%;
  height: auto;
  border: 1px solid #e5e5e5;
  padding: 10px;
  border-radius: 0.5rem;
}
//导航栏
#nav {
  border-bottom: 1px solid #e5e5e5;
  padding: 5px;
}
//弹框头部底部文本居中
/deep/.el-dialog__header {
  text-align: center !important;
}
/deep/.el-dialog__footer {
  text-align: center !important;
}
// 新增用户弹框
</style>
   
