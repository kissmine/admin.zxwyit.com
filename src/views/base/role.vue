<template>
  <div>
      <h1>用户角色</h1>
      <el-card class="box-card">
        <p class="box-p"><i class="el-icon-circle-plus-outline"></i>新增角色</p>
        <template>
        <el-table
          :data="tableData"
          style="width: 100%">
          <el-table-column
            label="#"
            width="80">
            <!-- <template slot-scope="scope">
              <span style="margin-left: 10px">{{ scope.row.date }}</span>
            </template> -->
            <template slot-scope="scope">
              <span style="margin-left: 10px">{{ scope.row.date }}</span>
            </template>
          </el-table-column>
          <el-table-column label="角色名称">
            <template slot-scope="scope" width='400'>
              <el-tag size="medium">{{ scope.row.name }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column label="操作">
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
      </template>


        <!-- <p slot="header" class="box-p">+新增角色</p> -->
        <!-- <div slot="header" class="clearfix">
          <span>卡片名称</span>
          <el-button style="float: right; padding: 3px 0" type="text">操作按钮</el-button>
        </div> -->
        <!-- <div slot="header" class="clearfix">
          <span>卡片名称</span>
          <el-button style="float: right; padding: 3px 0" type="text">操作按钮</el-button>
        </div> -->
        <!-- <div v-for="o in 4" :key="o" class="text item">
          {{'列表内容 ' + o }}
        </div> -->
      </el-card>
      
  </div>
</template>

<script>
export default {
    data() {
      return {
        tableData: [{
          date: '1',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄'
        }, {
          date: '3',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄'
        }, {
          date: '4',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        }]
      }
    },
    methods: {
      handleEdit(index, row) {
        console.log(index, row);
      },
      handleDelete(index, row) {
        console.log(index, row);
      }
    }
}
</script>

<style scoped>
/* 加号图标 */
.el-icon-circle-plus-outline{
  margin-right: 6px;
}
/* 新增角色 */
.box-p{
  color: rgb(21, 116, 226);
  padding: 0px 0px 20px 20px;
  font-size: 15px;
  margin: 0px;
  margin-bottom: 13px;
  border-bottom: 1px solid #EBEEF5;
}
/* 卡片框架 */
/* /deep/.el-card__body{

  padding: 20px 0px;
} */
/* 表格标题 */
/deep/.el-table th>.cell{
  padding-left: 17px;
}
  /* .box-p{
    margin-top: 0px;
    border-bottom: 1px solid #ccc; 
  }

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
    clear: both
  } */

  /* .box-card {
    width: 480px;
  } */
</style>>