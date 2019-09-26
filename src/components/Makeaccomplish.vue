<template>
  <!-- 3、完成制作 -->
  <div class="accomplish">
    <h1>{{this.Testscored[1].tpTitle}}</h1>
    <table class="table">
      <thead>
        <tr>
          <th>科目</th>
          <th>选择题</th>
          <th>填空题</th>
          <th>问答题</th>
          <th>总分</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td scope="row">{{this.Testscored[1].courseName}}</td>
          <td>
            <el-button type="danger" circle>{{this.Testscored[0].cumchoice}}'</el-button>
          </td>
          <td>
            <el-button type="danger" circle>{{this.Testscored[0].cumgap}}'</el-button>
          </td>
          <td>
            <el-button type="danger" circle>{{this.Testscored[0].cumessay}}'</el-button>
          </td>
          <td>
            <el-button type="danger" circle>{{this.Testscored[0].cumdataUp}}'</el-button>
          </td>
        </tr>
        <tr>
          <td scope="row" colspan="5">
            <el-button type="primary" @click="accoMplish">上一步</el-button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props:["Testscores"],
  data() {
    return {
      Testscored:[{
        cumchoice:"",//选择题总分
        cumgap:"",//填空题总分
        cumessay:"",//问答题总分
        cumdataUp:""//所有题目的总分
      },{
        tpTitle:"试卷名称",//试卷名称
        courseName:"科目"//科目
      }]
    };
  },
  watch:{
    Testscores:function(newVal,oldVal){
      if(newVal!='undefined'){
        this.Testscored = newVal;
      }
    }
  },
  mounted() {
    // console.log(this.Testscored[1].courseName)//用作检测是否有数据传过来
  },
  methods: {
    accoMplish() {//上一步
      var _this = this;
      _this.$emit("laststep");
    }
  }
};
</script>

<style lang="scss" scoped>
.accomplish {
  text-align: center;
  width: 100%;
  h1 {
    margin: 20px 0;
  }
  .table {
    width: 100%;
    border: 1px solid #ddd;
    border-collapse: collapse;
    margin-bottom: 50px;
    font-size: 14px;
  }
  .table thead tr th,
  .table tbody tr td {
    padding: 15px 12px;
    text-align: center;
    color: #333;
    border: 1px solid #ddd;
    border-collapse: collapse;
    background-color: #fff;
    .el-button.is-circle {
      padding: 3px 5px;
    }
  }
}
</style>