<template>
  <el-row class="warp">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><span class="fa fa-home"><b>  首页</b></span></el-breadcrumb-item>
        <el-breadcrumb-item>质量管理</el-breadcrumb-item>
        <el-breadcrumb-item>质量分类</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main">
      <h2>神马也没有！</h2>
    </el-col>
  </el-row>
</template>
<script>
  export default{
    data(){
      return {
        msg: 'hello vue'
      }
    }
  }
</script>
