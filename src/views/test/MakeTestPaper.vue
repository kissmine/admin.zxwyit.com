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
                    <MakeTestpaper  @geNext="next"/>
                </div>
                <div class="course1" v-if="active==1">
                    <Addthetitle @geNext="next"/>
                </div>
                <div class="course" v-show="active==2">
                    <h1>完成</h1>
                </div>
            </el-main>
        </el-container>
    </div>
</template>

<script>
import MakeTestpaper from "@/components/MakeTestpaper"
import Addthetitle from "@/components/Addthetitle"
  export default {
    components:{
        MakeTestpaper,
        Addthetitle
    },
    data() {
      return {
        active: 0
      };
    },
    methods: {
      next() {
        if (this.active++ > 2) this.active = 0;
      }
    }
  }
</script>

<style lang="scss" scoped>
#makeForm {
  .header {
    padding: 20px 0;
    border-bottom: 1px solid #ebeef5;
    box-sizing: border-box;
    margin-bottom: 20px;
    text-align: left;
  }
  .course {
    width: 50%;
    margin: 0 auto;
  }
  .course1{
    text-align: left;
  }
}
</style>