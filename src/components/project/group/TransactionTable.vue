<template>
  <el-table :data="data_list" style="width: 100%;padding-top: 15px;" v-loading="loading">
    <el-table-column label="测试类型" show-overflow-tooltip width="120">
      <template slot-scope="scope">
        {{scope.row.test_type}}
      </template>
    </el-table-column>
    <el-table-column label="项目id" show-overflow-tooltip width="80">
      <template slot-scope="scope">
        {{scope.row.project_id}}
      </template>
    </el-table-column>
    <el-table-column label="项目名称" show-overflow-tooltip>
      <template slot-scope="scope">
        {{scope.row.project_name}}
      </template>
    </el-table-column>
    <!-- <el-table-column label="涉及模块" width="195" align="center">
      <template v-for="(item, index) in data_list" slot-scope="scope">
        <div v-for="value in scope.row.service_list">
          <span>{{ value.service_name }}</span>
        </div>
      </template>
    </el-table-column> -->
    <el-table-column label="测试状态" align="right" >
      <template slot-scope="scope">
        <el-tag> {{formatStatus(scope.row.project_status)}}</el-tag>
      </template>
    </el-table-column>
    <el-table-column label="报告详情" align="right" show-overflow-tooltip>
      <template slot-scope="scope">
        <router-link :to="{
                            path: '/project/show/report_list/'+scope.row.project_id,
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
</template>

<script>
// import { fetchList } from '@/api/transaction'
import API2 from '../../../api/api_project'

export default {
  props:['project_name'],
  data() {
    return {
      filters: {
        name: ''
      },
      loading: true,
      // result: { data_list: [], errno:0 },
      total: 0,
      page: 1,
      limit: 10
    }
  },
  created() {
    this.project_id = this.$route.query.project_id;//获取上个页面传递的id,在下面获取数据的时候先提交id
  },
  methods: {
    //状态显示转换
    formatStatus: function (project_status) {
      return project_status == 2 ? '已完成' : project_status == 1 ? '测试中' : '开发中';
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
        project_id: that.project_id
      };

      that.loading = true;
      API2.findTestList(params).then(function (result) {
        that.loading = false;
        if (result && result.data_list) {
          that.total = result.total;
          that.data_list = result.data_list;
        }
        // console.log(result.data_list)
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
