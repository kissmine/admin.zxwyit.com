<template>
  <!-- 1、选择题 -->
  <el-form
    :model="choiceForm"
    status-icon
    :rules="addrules"
    ref="choiceForm"
    @submit.native.prevent
  >
    <el-form-item label="题干" prop="questionTitle" :label-width="formLabelWidth">
      <el-input type="textarea" v-model="choiceForm.questionTitle" autocomplete="off"></el-input>
    </el-form-item>
    <ul class="redio-ul">
      <li v-for="(item,index) in choiceForm.chooseQuestion" :key="index">
        <el-checkbox v-model="item.cqIsRight">{{ liList[index] }}、</el-checkbox>
        <el-input type="text" v-model="item.cqOption" autocomplete="off"></el-input>
        <el-button
          type="danger"
          icon="el-icon-delete"
          circle
          @click="deleteLi(index)"
          v-show="choiceForm.chooseQuestion.length>2"
        ></el-button>
      </li>
    </ul>
    <el-form-item label="分值" :label-width="formLabelWidth">
      <el-input-number v-model="choiceForm.tpqScore" :min="5" :max="10" label="描述文字"></el-input-number>
    </el-form-item>
    <el-form-item :label-width="formLabelWidth">
      <el-button round @click="resetForm">重置</el-button>
      <el-button
        type="info"
        round
        @click="addLi"
        :disabled="choiceForm.chooseQuestion.length>=liList.length"
      >新增选项</el-button>
      <el-button
        type="primary"
        round
        icon="el-icon-document-checked"
        @click="AddChoice('choiceForm')"
      >保存题目</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  data() {
    return {
      formLabelWidth: "100px", //表单lable宽度
      choiceForm: {
        tpqScore: 5, //分值
        questionTitle: "", //题目的标题
        questionTypeId: 1, //题目的类型 1=选择题 2=填空题 3=问答题
        chooseQuestion: [//设置默认为四个选项
          {
            cqOption: "", //选项内容
            cqIsRight: false //是否为正确答案 true:正确答案 false：不是
          },
          {
            cqOption: "",
            cqIsRight: false
          },
          {
            cqOption: "",
            cqIsRight: false
          },
          {
            cqOption: "",
            cqIsRight: false
          }
        ]
      },
      setChoice: {
        //用来做重置
        tpqScore: 5,
        questionTitle: "",
        questionTypeId: 1,
        chooseQuestion: [
          {
            cqOption: "",
            cqIsRight: false
          },
          {
            cqOption: "",
            cqIsRight: false
          },
          {
            cqOption: "",
            cqIsRight: false
          },
          {
            cqOption: "",
            cqIsRight: false
          }
        ]
      },
      addrules: {
        questionTitle: [
          { required: true, message: "请输入标题", trigger: "blur" }
        ]
      },
      liList: ["A", "B", "C", "D", "E", "F"] //用作添加选项、删除选项
    };
  },
  methods: {
    //添加题目
    AddChoice(iceform) {
      var _this = this;
      var bool = false;
      // console.log(_this.$refs[iceform])
      _this.$refs[iceform].validate(valid => {
        if (valid) {
          _this.choiceForm.chooseQuestion.forEach(item => {
            if (item.cqIsRight) {
              bool = true;
            }
          });
          if (bool) {
            _this.$emit("setChoice", _this.choiceForm);
            // 重新定义变量,解除绑定
            let choice = JSON.parse(JSON.stringify(_this.setChoice));
            // 提交成功后重置
            _this.choiceForm = choice;
            // _this.$refs['essayForm'].resetFields() //会把传入的参数清空
          } else {
            _this.$message({
              message: "最少勾选一个答案",
              type: "warning"
            });
          }
        }
      });
    },
    //删除
    deleteLi(index) {
      var _this = this;
      _this.choiceForm.chooseQuestion.splice(index, 1);
    },
    //新增
    addLi() {
      var _this = this;
      _this.choiceForm.chooseQuestion.push({ cqOption: "", cqIsRight: false });
    },
    //重置
    resetForm() {
      var _this = this;
      _this.choiceForm = JSON.parse(JSON.stringify(_this.setChoice));
    }
  }
};
</script>

<style lang="scss" scoped>
.redio-ul {
  li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 10px 20px;
    .el-button {
      margin-left: 15px;
    }
  }
}
</style>