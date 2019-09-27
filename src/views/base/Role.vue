<template>
  <div id="table-Role">
      <el-card class="box-card">
        <!-- 新增角色 -->
        <p class="box-p"><span @click="add()" ><i class="el-icon-circle-plus-outline"></i>新增角色</span></p>
        <!-- 新增角色弹框-->
        <el-dialog title="新增角色信息" :visible.sync="dialogFormVisible">
          <el-form :model="Form">
            <el-form-item label="角色名称" :label-width="formLabelWidth">
              <el-input v-model="Form.name" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="cancel">取 消</el-button>
            <el-button type="primary" @click="affirm">确 定</el-button>
          </div>
        </el-dialog>
        <!-- 编辑角色弹框 -->
        <el-dialog title="修改角色信息" :visible.sync="modifier">
          <el-form >
            <el-form-item label="角色名称" :label-width="formLabelWidth">
              <el-input  v-model="unames" autocomplete="off"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="cancel">取 消</el-button>
            <el-button type="primary" @click="affirms">确 定</el-button>
          </div>
        </el-dialog>
        <!-- 用户信息表格 -->
        <template>
          <el-table
            :data="tableData"
            style="width: 100%">
            <el-table-column
              label="#"
              width="140">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.$index+1 }}</span>
              </template>
            </el-table-column>
            <el-table-column label="角色名称">
              <template slot-scope="scope" width='400'>
                {{ scope.row.userTypeTypeName }}
              </template>
            </el-table-column>
            <el-table-column label="操作" width="250">
              <template slot-scope="scope">
                <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                <el-button size="mini" type="danger" @click="handleDelete(scope.$index, scope.row)" :disabled='scope.row.disable'>删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </template>
      </el-card>
  </div>
</template>

<script>
export default {
    data() {
      return {
        modifier:false,     //编辑角色
        dialogFormVisible: false, //新增角色框是否弹出，false不弹出，true弹出
        tableData:[],   //请求的角色信息，用一个空数组接受
        Form: {
          name: ''  //新增角色输入框的值
        },
        uname:'',   //编辑角色的信息
        unames:'',   //编辑角色名称
        formLabelWidth: '100px' //"角色信息"文字的宽度
      }
    },
    methods: {
      // 新增角色
      add(){
        var than=this
        than.dialogFormVisible=true
      },
      // 取消
      cancel(){
        var than=this
        than.dialogFormVisible=false
        than.modifier=false
      },
      // 新增角色确认
      affirm(){
        var than=this
        var chin=/^[\u4e00-\u9fa5]{0,}$/
        if(chin.test(than.Form.name)){
          than.axios.post('/UserType/AddUserRole?userRoleName='+than.Form.name,
          ).then(function(res){
            if(res.data.code==1){
              than.$message({
              type: 'success',
              message: '添加成功！'
              });
              than.RoleInfo()
              than.dialogFormVisible=false
            }else{
              than.$message.error('添加失败！');
            }
          })
        }else{
          than.$message.error('名称必须是中文！');
        }
      },
      // 编辑
      handleEdit(index, row) {
        var than=this
        than.modifier=true
        than.uname=row
        than.unames=row.userTypeTypeName
      },
      // 编辑角色确认
      affirms(){
        var than=this
        var chin=/^[\u4e00-\u9fa5]{0,}$/  //中文正则表达式
        if(chin.test(than.unames)){
          than.axios.post('/UserType/ModifyUserRole?id='+than.uname.userTypeId+'&userRoleName='+than.unames,
          ).then(function(res){
            if(res.data.code==1){
              than.$message({
              type: 'success',
              message: '修改成功！'
              });
              than.modifier=false
            }else{
              than.$message.error('修改失败！');
            }
            than.RoleInfo()
          })
        }else{
          than.$message.error('名称必须是中文！');
        }
      },
      // 删除
      handleDelete(index, row) {
        var than=this;
        this.$confirm('此操作将永久删除该角色, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          than.axios.post('/UserType/RemoveUserRole?userRoleId='+row.userTypeId,
          ).then(function(res){
            if(res.data.code==1){
              than.$message({
              type: 'success',
              message: '删除成功！'  
              });
              than.RoleInfo()
            }else{
              than.$message.error('删除失败！');
            }
          })
        }).catch(() => {
          than.$message({
            type: 'info',
            message: '已取消删除'
          });          
        });
      },
      // 请求全部角色信息
      RoleInfo() {
        var than=this
        this.axios.get('/UserType/GetUserRoles')
        .then(function(res){
          than.tableData=res.data
        })
      }
    },
    // 创建后
    created(){
      this.RoleInfo()
    }
}
</script>

<style lang="less" scoped>
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
/* 悬停新增角色，不让选中文字 */
.box-p:hover{
    cursor: default;
    user-select:none;
}
/* 新增角色弹出框 */
/deep/.el-dialog {
  width: 32%;
  text-align: center;
}
/* 新增角色弹出框里面的取消和确认 */
/deep/.el-dialog__footer{
  text-align: center;
}
/* 表格标题 */
/deep/.el-table th>.cell{
  padding-left: 17px;
}

</style>>
