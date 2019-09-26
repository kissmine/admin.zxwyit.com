<template>
  <div id="ClassManage">
    <!-- 渲染 -->
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <i class="el-icon-circle-plus-outline" style="margin-right:5px"></i>
        <span @click="addButton">新增班级</span>
      </div>
      <el-table :data="tableData" style="width: 100%;">
        <el-table-column label="#">
          <template slot-scope="scope" style="padding:0px">
            <span>{{ (scope.$index)+1 }}</span>
          </template>
        </el-table-column>
        <el-table-column label="班级名称">
          <template slot-scope="scope">
            <span>{{ scope.row.className }}</span>
          </template>
        </el-table-column>
        <el-table-column label="授课老师">
          <template slot-scope="scope">
            <span>{{ scope.row.userName }}</span>
          </template>
        </el-table-column>
        <el-table-column label="专业">
          <template slot-scope="scope">
            <span>{{ scope.row.courseName }}</span>
          </template>
        </el-table-column>
        <el-table-column label="班级人数">
          <template slot-scope="scope">
            <span>{{ scope.row.classStudents }}</span>
          </template>
        </el-table-column>
        <el-table-column label="开班日期">
          <template slot-scope="scope">
            <span>{{scope.row.classCreateTime}}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="145">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <el-button
              size="mini"
              type="danger"
              @click="handleDelete(scope.$index, scope.row)"
              :disabled="scope.row.classStudents >0 ? true: false "
            >删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 增加 -->
      <el-dialog title="新增班级" :visible.sync="centerDialogVisible" width="30%" center>
        <el-form :model="RuleForm" ref="RuleForm" label-width="100px" class="demo-ruleForm">
          <el-form-item label="班级名称" prop="name">
            <el-input v-model="RuleForm.className"></el-input>
          </el-form-item>
          <el-form-item label="专业课程" prop="majorCouse">
            <el-select v-model="RuleForm.majorCouse" placeholder="请选择课程">
              <el-option
                v-for="(item,index) in allCourse"
                :key="index"
                :label="item.courseName"
                :value="item.courseId"
              ></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="授课老师" prop="teacherName">
            <el-select v-model="RuleForm.teacherName" placeholder="请选择老师">
              <el-option
                v-for="(item,index) in allTeacher"
                :key="index"
                :label="item.userName"
                :value="item.userId"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-form>

        <span slot="footer" class="dialog-footer">
          <el-button @click="centerDialogVisible = false">取 消</el-button>
          <el-button type="primary" @click="addClass">确 定</el-button>
        </span>
      </el-dialog>
      <!-- 修改 -->
      <el-dialog title="修改班级" :visible.sync="Modalboxdisplay" width="30%" center>
        <el-form
          :model="TwowayBinding"
          ref="TwowayBinding"
          label-width="100px"
          class="demo-ruleForm"
        >
          <el-form-item label="班级名称" prop="name">
            <el-input v-model="TwowayBinding.className" value :placeholder="Hold_className"></el-input>
          </el-form-item>

          <el-form-item label="专业课程" prop="majorCouse">
            <el-select v-model="TwowayBinding.majorCouse" :placeholder="Hold_courseName">
              <el-option
                v-for="(item,index) in allCourse"
                :key="index"
                :label="item.courseName"
                :value="item.courseId"
              ></el-option>
            </el-select>
          </el-form-item>

          <el-form-item label="授课老师" prop="teacherName">
            <el-select v-model="TwowayBinding.teacherName" :placeholder="Hold_userName">
              <el-option
                v-for="(item,index) in allTeacher"
                :key="index"
                :label="item.userName"
                :value="item.userId"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-form>

        <span slot="footer" class="dialog-footer">
          <el-button @click="Modalboxdisplay = false">取 消</el-button>
          <el-button type="primary" @click="alterClass">确 定</el-button>
        </span>
      </el-dialog>
    </el-card>
  </div>
</template>


<script>
export default {
  data() {
    return {
      tableData: [], //所有班级信息数据
      allCourse: [], //所有专业名字数据
      allTeacher: [], //所有老师名字数据
      Hold_userName: [], //暂存老师名字数据
      Hold_courseName: [], //暂存专业名字数据
      Hold_className: [], //暂存班级名称数据
      Modalboxdisplay: false, //修改模态框隐藏
      centerDialogVisible: false, //新增模态框隐藏
      //增加双向绑定
      RuleForm: {
        className: "",
        majorCouse: "",
        teacherName: ""
      },
      //修改双向绑定
      TwowayBinding: {
        className: "", //班级名字
        majorCouse: "", //专业
        teacherName: "", //老师
        classId: "" //班级id
      }
    };
  },
  methods: {
    //修改
    //修改按钮
    handleEdit(index, row) {
      var _this = this;
      _this.Modalboxdisplay = true;//打开模态框
      _this.TwowayBinding.classId = row.classId; //班级id
      _this.Hold_userName = row.userName; //暂存老师名字
      _this.Hold_courseName = row.courseName; //暂存专业名字
      _this.Hold_className = row.className; //暂存班级名字
    },
    //修改模态框确认按钮
    alterClass() {
      var _this = this;
      //判断不能为空
      if (
        _this.TwowayBinding.className != "" &&
        _this.TwowayBinding.majorCouse != "" &&
        _this.TwowayBinding.teacherName != "" &&
        _this.TwowayBinding.classId != ""
      ) {
        _this.axios
          .post("/Class/ModifyClass", {
            className: _this.TwowayBinding.className,
            classCourseId: _this.TwowayBinding.majorCouse,
            classTeacherId: _this.TwowayBinding.teacherName,
            classId: _this.TwowayBinding.classId
          })
          .then(res => {
            if (res.data.code == 1) {
              _this.queryClass(); //实时刷新
              _this.$message({
                message: "添加成功",
                type: "success"
              });
              _this.Modalboxdisplay = false;//退出模态框
            } else if (res.data.code == -1) {
              _this.$message.error("不可添加");
            } else if (res.data.code == -2) {
              _this.$message.error("格式错误，请重新确认");
            } else {
              _this.$message.error("系统错误，请联系管理员");
            }
            //退出时清空值
            _this.TwowayBinding.className = "";
            _this.TwowayBinding.majorCouse = "";
            _this.TwowayBinding.teacherName = "";
            _this.TwowayBinding.classId = "";
          })
          .catch(function(error) {
            console.log(error);
          });
      } else {
        _this.$message.error("不能有一项为空，请完善");
      }
    },
    //新增
    //新增按钮
    addButton() {
      var _this = this;
      _this.centerDialogVisible = true;//打开模态框
    },
    //新增模态框确认按钮
    addClass() {
      var _this = this;
      //判断不能为空
      if (
        _this.RuleForm.className != "" &&
        _this.RuleForm.majorCouse != "" &&
        _this.RuleForm.teacherName != ""
      ) {
        this.axios
          .post("/Class/AddClass", {
            className: _this.RuleForm.className,//班级名字
            classCourseId: _this.RuleForm.majorCouse,//专业名字
            classTeacherId: _this.RuleForm.teacherName//老师名字
          })
          .then(res => {
            if (res.data.code == 1) {
              _this.queryClass(); //实时刷新
              _this.$message({
                message: "添加成功",
                type: "success"
              });
              _this.centerDialogVisible = false;//退出模态框
            } else if (res.data.code == -1) {
              _this.$message.error("不可添加");
            } else if (res.data.code == -2) {
              _this.$message.error("格式错误，请重新确认");
            } else {
              _this.$message.error("系统错误，请联系管理员");
            }
          })
          .catch(function(error) {
            _this.$message.error("系统错误，请联系管理员");
          });
      } else {
        _this.$message.error("不能为空，必须为中文");
      }
    },
    //删除
    handleDelete(index, row) {
      var _this = this;
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          _this.axios
            .get(
              "http://192.168.1.188:12/api/Class/RemoveClass?classId=" + row.classId,)
            .then(res => {
              if (res.data.code == 1) {
                _this.queryClass(); //实时刷新
                _this.$message({
                  message: "删除成功",
                  type: "success"
                });
              } else if (res.data.code == -1) {
                _this.$message.error("不可删除");
              } else {
                _this.$message.error("删除失败");
              }
            })
            .catch(function(error) {
              _this.$message.error("系统错误，请联系管理员");
            });
        })
        .catch(() => {
          _this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    },
    //查询所有班级
    queryClass() {
      var _this = this;
      _this.axios
        .get("/Class/GetAllClass")
        .then(res => {
          //开班日期只留下年月日
          for (var i = 0; i < res.data.length; i++) {
            //循环出编号
            res.data[i].id = i + 1;
            res.data[i].classCreateTime = new Date(
            res.data[i].classCreateTime
            ).toLocaleDateString();
          }
          _this.tableData = res.data;
        })
        .catch(function(error) {
           _this.$message.error("系统错误，请联系管理员");
        });
    }
  },
  //渲染
  created() {
    var _this=this
    //调用查询
    _this.queryClass();
    //获取所有专业
    _this.axios.get("/Class/GetAllCourse").then(res => {
      _this.allCourse = res.data;
    });
    //获取所有老师
    _this.axios.get("/User/GetTeachers").then(res => {
      _this.allTeacher = res.data;
    });
  }
};
</script>

<style lang="scss" scoped>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
.name-wrapper {
  width: 14%;
}
.clearfix {
  color: #409eff;
}
.el-input {
  width: auto;
}
.box-card {
  z-index: 666;
}
.el-button {
  width: 45%;
  text-align: center;
}
</style>