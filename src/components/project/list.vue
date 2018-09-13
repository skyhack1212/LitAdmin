<template>
  <el-row class="warp">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><span class="fa fa-home"><b>  首页</b></span></el-breadcrumb-item>
        <el-breadcrumb-item>项目管理</el-breadcrumb-item>
        <el-breadcrumb-item>项目列表</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main" v-loading="loading" element-loading-text="拼命加载中">
      <!--工具条-->
      <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
        <el-form :inline="true" :model="filters">
          <el-form-item>
            <el-input v-model="filters.name" placeholder="项目ID/项目名称" style="min-width: 240px;" @keyup.enter.native="handleSearch"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="handleSearch">查询</el-button>
          </el-form-item>
        </el-form>
      </el-col>
      <!--工具条-->
      <el-col :span="24" class="toolbar">
        <!-- <el-button type="danger" @click="batchDeleteBook" :disabled="this.sels.length===0">批量删除</el-button> -->
        <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total"
                       style="float:right;">
        </el-pagination>
      </el-col>
      <!--列表-->
      <el-table :data="data_list" highlight-current-row v-loading="loading" style="width: 100%;">
        <!-- <el-table-column type="index" width="60"> -->
        <el-table-column prop="id" label="id" width="100" sortable>
        </el-table-column>
        <el-table-column prop="project_id" label="项目id" width="120" sortable>
        </el-table-column>
        <el-table-column prop="project_name" label="项目名称"  min-width="80" sortable>
        </el-table-column>
        <!-- <el-table-column prop="service_list" label="服务列表" sortable>
        </el-table-column> -->
        <el-table-column label="服务模块" >
          <!-- 数据的遍历  scope.row就代表数据的每一个对象-->
          <template v-for="(item, index) in data_list" slot-scope="scope">
            <div v-for="value in scope.row.service_list">
              <span>{{ value.service_name }}</span>
            </div>
          </template>
        </el-table-column>
        <el-table-column prop="project_status" label="项目状态" width="160" :formatter="formatStatus" sortable>
        </el-table-column>
        <el-table-column prop="rd" label="RD" width="120" sortable>
        </el-table-column>
        <el-table-column prop="qa" label="QA" width="120" sortable>
        </el-table-column>
        <el-table-column prop="create_time" label="项目时间" width="160" sortable>
        </el-table-column>
        <el-table-column label="操作" width="150">
          <template slot-scope="scope">
            <router-link :to="{
                                  path: '/project/show/' + scope.row.project_id,
                                  query: {
                                      project_id: scope.row.project_id,
                                      project_name: scope.row.project_name
                                  }
                              }">
              <el-button type="primary" plain size="mini">查看</el-button>
            </router-link>
          </template>
        </el-table-column>
      </el-table>
    </el-col>
  </el-row>
</template>

<script>
  import API2 from '../../api/api_project'

  export default {
    data() {
      // result={ data_list: [], errno: 0 };
      return {
        filters: {
          name: ''
        },
        loading: false,
        // result: { data_list: [], errno:0 },
        total: 0,
        page: 1,
        limit: 10,
        loading: false
      }
    },
    methods: {
      //状态显示转换
      formatStatus: function (row, column) {
        return row.project_status == 2 ? '已完成' : row.project_status == 1 ? '测试中' : '开发中，待提测';
      },
      handleCurrentChange(val) {
        this.page = val;
        this.search();
      },
      handleSearch(){
        this.total = 0;
        this.page = 1;
        this.search();
      },
      //获取项目列表
      search() {
        let that = this;
        let params = {
          page_num: that.page,
          page_limit: 10,
          // name: that.filters.name
          project_id_list: []
        };

        that.loading = true;
        API2.findProjectList(params).then(function (result) {
          that.loading = false;
          if (result && result.data_list) {
            that.total = result.total;
            that.data_list = result.data_list;
          }
          console.log(result.data_list)
        }, function (err) {
          that.loading = false;
          that.$message.error({showClose: true, message: err.toString(), duration: 2000});
        }).catch(function (error) {
          that.loading = false;
          console.log(error);
          that.$message.error({showClose: true, message: '请求出现异常', duration: 2000});
        });
      }
    },
    mounted() {
      this.handleSearch()
    }
  }
</script>

<style scoped>

</style>
