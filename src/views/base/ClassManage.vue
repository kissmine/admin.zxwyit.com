<template>
  <div>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span class="el-icon-circle-plus-outline">新增班级</span>
      </div>
      <el-table :data="tableData" style="width: 100%">
        <el-table-column label="#">
          <template slot-scope="scope">
            <span style="margin-left: 10px">{{ (scope.$index)+1 }}</span>
          </template>
        </el-table-column>
        <el-table-column label="班级名称">
          <template slot-scope="scope">
            <span style="margin-left: 10px">{{ scope.row.className }}</span>
          </template>
        </el-table-column>
        <el-table-column label="授课老师">
          <template slot-scope="scope">
            <span style="margin-left: 10px">{{ scope.row.userName }}</span>
          </template>
        </el-table-column>
        <el-table-column label="专业">
          <template slot-scope="scope">
            <span style="margin-left: 10px">{{ scope.row.courseName }}</span>
          </template>
        </el-table-column>
        <el-table-column label="班级人数">
          <template slot-scope="scope">
            <span style="margin-left: 10px">{{ scope.row.classStudents }}</span>
          </template>
        </el-table-column>
        <el-table-column label="开班日期">
          <template slot-scope="scope">
            <span style="margin-left: 10px">{{scope.row.classCreateTimea}}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
            <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
  </div>
</template>


<script>
export default {
  data() {
    return {
      tableData: [],
      id: []
    };
  },
  methods: {
    handleEdit(index, row) {
      console.log(index, row);
    },
    handleDelete(index, row) {
      console.log(index, row);
    },
    //删除
    handleDelete(index, row) {
      var _this = this;
      // console.log(row.classId)
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.axios
            .get(
              "http://192.168.1.188:12/api/Class/RemoveClass?classId=" +
                row.classId +
                ""
            )
            .then(res => {
              //  if(res.code == -1) {
              // 		_this.$message({
              // 			message: '删除成功',
              // 			type: 'success'
              // 		});
              // 	} else {
              // 		_this.$message.error('删除失败');
              //   }
              res.data;
              console.log(res.data);
            })
            .catch(function(error) {
              console.log(error);
            });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    }
  },
  //渲染
  created() {
    var _this = this;
    this.axios
      .get("/Class/GetAllClass")
      .then(res => {
        _this.tableData = res.data;
      })
      .catch(function(error) {
        console.log(error);
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
.el-icon-circle-plus-outline {
  color: #409eff;
}
</style>