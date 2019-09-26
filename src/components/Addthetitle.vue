<template>
  <!-- 2、添加题目 -->
  <div class="redios">
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
    <div class="redio_buttom">
      <Topicpreview :TestPaper="TestPaper" @AllsumUp="AllsumUp"></Topicpreview>
    </div>
  </div>
</template>

<script>
import AddthetitleMcq from "@/components/Examinationpaper/AddthetitleMcq";
import Addfillinblanks from "@/components/Examinationpaper/Addfillinblanks";
import Addquestionanswer from "@/components/Examinationpaper/Addquestionanswer";
import Topicpreview from "@/components/Examinationpaper/Topicpreview";
export default {
  components: {
    AddthetitleMcq, //选择题
    Addfillinblanks, //填空题
    Addquestionanswer, //问答题
    Topicpreview //试卷总预览与修改
  },
  data() {
    return {
      radio: 1, //题目选项
      TestPaper: [],//所有题目的信息
      Testscores:[],//接收获取试卷的信息
      stycores:null//用于接收子组件传过来的题目分值
    };
  },
  mounted() {
    this.GetTestPaper();
  },
  methods: {
    /**
     *获取试卷信息
     * */ 
    GetTestPaper() {
      let _this = this;
      var testPaperId = sessionStorage.getItem("testPaperId");
      if (testPaperId) {
        _this.axios
          .get("/TestPaper/GetTestPaper", {
            params: {
              id: testPaperId
            }
          })
          .then(res => {
            if (res.data) {
              _this.Testscores=res.data
              _this.TestPaper = res.data.questions;
            }
          })
          .catch(error => {
            console.log(error);
          });
      }
    },
     /**
     * 用来做添加选择题、填空题、问答题
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
          _this.GetTestPaper();
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
      _this.$message({//确定后提示语句
        message: message,
        type: type
      });
    },
    AllsumUp(datc){//接受所有题目的分值和总分
      this.stycores = [datc,this.Testscores]
    },
    /**
     * 下一步
     * */ 
    submitMake() {
      let _this = this;
      _this.GetTestPaper()
      if(_this.Testscores.questions.length>=1&&_this.Testscores.tpTitle!="undefined"){
        _this.$emit("secondgear",this.stycores);
      }else{
        this.$message({
          message: '请至少填一道题！OK>?----O(∩_∩)O哈哈~',
          type: 'warning'
        });
      }
    }
  }
};
</script>

<style lang="scss" scoped>
.redios {
  border: 1px solid #ECECEC;
  box-shadow: 0px 0px 10px 5px #ECECEC;
  // padding: 20px 0px;
  margin: 4px 4px;
  .redio {
    line-height: 70px;
    border-bottom: #ECECEC 1px solid;
    overflow: hidden;
    span {
      padding-left: 20px;
      padding-right: 20px;
      line-height: 30px;
    }
    .el-button--primary{
      float: right;
      margin: 15px 30px 15px 0px ;
    }
  }
  .redio-content{
    padding: 20px 20px;
    border-bottom: #ECECEC 1px solid;
  }
  .redio_buttom{
    padding: 20px;
  }
}
</style>
