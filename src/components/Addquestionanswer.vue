<template>
  <el-form :model="essayForm" status-icon ref="essayForm" @submit.native.prevent>
    <el-form-item label="题干" prop="questionTitle" :label-width="formLabelWidth">
      <el-input type="textarea" :rows="1" placeholder="请输入内容" v-model="essayForm.questionTitle" ></el-input>
    </el-form-item>
    <el-form-item label="参考答案" prop="questionTitle" :label-width="formLabelWidth">
      <el-input type="textarea" :rows="1" placeholder="请输入内容" v-model="essayForm.answerQuestion.aqAnswer"></el-input>
    </el-form-item>
    <el-form-item label="分值" prop="questionTitle" :label-width="formLabelWidth">
      <el-input-number v-model="essayForm.tpqScore" :min="5" :max="10" label="描述文字"></el-input-number>
    </el-form-item>
    <el-form-item :label-width="formLabelWidth">
      <el-button round @click="resetForm('essayForm')">重置</el-button>
      <el-button
        type="primary"
        round
        icon="el-icon-document-checked"
        @click="AddEssay('essayForm')"
      >保存题目</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  data() {
    return {
      formLabelWidth: "100px",
      essayForm: {
        tpqScore: 5,
        questionTitle: "",
        questionTypeId: 3,
        answerQuestion: {
          aqAnswer: ""//问答题的答案
        }
      },
      setEssay: { //用于重置
        tpqScore: 5,
        questionTitle: "",
        questionTypeId: 3,
        answerQuestion: {
          aqAnswer: ""
        }
      }
    };
  },
  methods: {
    resetForm(formName) {
      let _this = this;
      _this.$refs[formName].resetFields();
      _this.essayForm = JSON.parse(JSON.stringify(_this.setEssay));
    },
    AddEssay(formName){
        var _this=this
        _this.$refs[formName].validate(valid => {
            if(valid){
                _this.$emit("setEssay",_this.essayForm)
                _this.essayForm=JSON.parse(JSON.stringify(_this.setEssay))
            }
        })
    }
  }
};
</script>