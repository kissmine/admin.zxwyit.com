<template>
    <div id="makeForm">
        <el-container>
            <el-header>
                <el-steps :active="active" finish-status="success">
                    <el-step title="试卷信息"></el-step>
                    <el-step title="添加题目"></el-step>
                    <el-step title="完成制作"></el-step>
                </el-steps>
            </el-header>
            <el-main>
                <div class="course" v-show="active==0">
                    <MakeTestpaper  @firstgear="Sharedfile"/>
                </div>
                <!-- 由于添加题目内容较多，直接用v-if改变元素存在还是不存在 -->
                <div class="course1" v-if="active==1">
                    <Addthetitle @secondgear="Sharedfile"/>
                </div>
                <div class="course2" v-show="active==2">
                    <Makeaccomplish :Testscores="Testscores" @laststep="laststep"/>
                </div>
            </el-main>
        </el-container>
    </div>
</template>

<script>
import MakeTestpaper from "@/components/MakeTestpaper"
import Addthetitle from "@/components/Addthetitle"
import Makeaccomplish from "@/components/Makeaccomplish"
  export default {
    components:{
        MakeTestpaper,//试卷信息
        Addthetitle,//添加题目
        Makeaccomplish//完成制作
    },
    data() {
      return {
        active:0,//步骤
        Testscores:[]//用作接收计算好的题目分值
      };
    },
    methods: {
      /**
       * 步骤切换
       * @param {Object} data 传给完成制作的分值
      */
      Sharedfile(data) {
        if(data!="undefined"){
          this.Testscores=data
        }
        if (this.active++ > 2) this.active = 0;
      },
      laststep() {
        this.active--
      }
    }
  }
</script>

<style lang="scss" scoped>
#makeForm {
  border: 1px solid #ECECEC;
  box-shadow: 0px 0px 10px 5px #ECECEC;
  margin:20px 10px;
  .el-header {
    height: 90px !important;
    padding: 20px 50px;
    border-bottom: 1px solid #ebeef5;
    text-align: left;
  }
  .course {
    width: 50%;
    margin: 0 auto;
  }
  .course1{
    text-align: left;
  }
  .course2{
    width: 95%;
    margin: 0 auto;
  }
}
</style>