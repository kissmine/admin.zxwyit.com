<template>
  <div>
    <div class="redio">
      <span>题目类型</span>
      <template>
        <el-radio-group v-model="radio">
          <el-radio :label="1">选择题</el-radio>
          <el-radio :label="2">填空题</el-radio>
          <el-radio :label="3">问答题</el-radio>
        </el-radio-group>
        <el-button type="primary" @click="submitMake">完成制卷</el-button>
      </template>
    </div>
    <div class="redio-content">
      <AddthetitleMcq v-if="radio==1" @setChoice="addQuestion" />
      <Addfillinblanks v-if="radio==2" @setGap="addQuestion" />
      <Addquestionanswer v-if="radio==3" @setEssay="addQuestion" />
    </div>
    <Topicpreview :TestPaper="TestPaper"></Topicpreview>
    <div style="text-align: center;margin-top: 50px;"></div>
  </div>
</template>

<script>
import MakeTestpaper from "@/components/MakeTestpaper";
import AddthetitleMcq from "@/components/AddthetitleMcq";
import Addfillinblanks from "@/components/Addfillinblanks";
import Addquestionanswer from "@/components/Addquestionanswer";
import Topicpreview from "@/components/Topicpreview";
export default {
  components: {
    MakeTestpaper,
    AddthetitleMcq, //选择题
    Addfillinblanks, //填空题
    Addquestionanswer, //问答题
    Topicpreview //题目预览
  },
  data() {
    return {
      radio: 1, //题目选项
      TestPaper: []
    };
  },
  mounted() {
    // sessionStorage.setItem("testPaperId", 3124);
    this.GetTestPaper();
  },
  methods: {
    // 获取试卷信息
    GetTestPaper() {
      let _this = this;
      var testPaperId = sessionStorage.getItem("testPaperId");
      if (testPaperId) {
        _this.axios.get("/TestPaper/GetTestPaper", {
            params: {
              id: testPaperId
            }
          })
          .then(res => {
            if (res.data) {
              _this.TestPaper = res.data.questions;
              console.log(_this.TestPaper)
            }
          })
          .catch(error => {
            console.log(error);
          });
      }
    },
    /**
     * 添加选择题数据
     * @param {object} data 子组件向父组件传入的数据
     */
    addQuestion(data) {
      let _this = this;
      _this.axios.post("/TestPaper/AddQuestionToTestPaper", {
          tpqPaperId: sessionStorage.getItem("testPaperId"),
          tpqScore: data.tpqScore,
          tpqQuestion: data
        })
        .then(res => {
          let code = res.data.code; //返回代码
          let message = res.data.message; //消息
          let reData = res.data.data; //操作成功后，返回给前端有用的数据
          reData.redactShow = true;
          _this.formMessage(code, message);
          if (code == 1) {
            _this.TestPaper.push(reData);
          }
        })
        .catch(error => {
          console.log(error);
        });
    },
    /**
     * 操作表单提示消息
     * @param {Number} code 请求返回参数
     * @param {String} msg 请求返回参数
     */
    formMessage(code, msg) {
      let _this = this;
      let type = "warning";
      let message = "其它错误";
      // 返回代码 0：没有改变 1：成功 -1：系统异常 -2：参数错误 除此之外就是其它错误
      switch (code) {
        case -1:
          message = msg;
          break;
        case -2:
          message = msg;
          break;
        case 0:
          message = msg;
          type = "info";
          break;
        case 1:
          message = msg;
          type = "success";
          break;
        default:
          message = msg;
          break;
      }
      _this.$message({
        //确定后提示语句
        message: message,
        type: type
      });
    },
    // 下一步
    submitMake() {
      let _this = this;
      _this.$emit("geNext");
    }
  }
};
</script>

<style lang="scss" scoped>
.redio {
  span {
    padding-left: 20px;
    padding-right: 200px;
    line-height: 30px;
  }
  .el-button{
    float: right;
  }
}
</style>
