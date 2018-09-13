<template>
  <el-table :data="datalist" style="width: 100%;padding-top: 15px;">
    <el-table-column label="执行时间" show-overflow-tooltip>
      <template slot-scope="scope">
        {{scope.row.create_time}}
      </template>
    </el-table-column>
    <el-table-column label="项目id" show-overflow-tooltip>
      <template slot-scope="scope">
        {{scope.row.project_id}}
      </template>
    </el-table-column>
    <el-table-column label="服务名称" show-overflow-tooltip>
      <template slot-scope="scope">
        {{scope.row.service_name}}
      </template>
    </el-table-column>
    <el-table-column label="执行状态" width="100" align="center" >
      <template slot-scope="scope">
        <el-tag v-if="scope.row.is_pass == 1" type="success">
            {{formatStatus(scope.row.is_pass)}}
        </el-tag>
        <el-tag v-else type="danger">
            {{formatStatus(scope.row.is_pass)}}
        </el-tag>
      </template>
    </el-table-column>
    <el-table-column label="报告详情" align="right" show-overflow-tooltip>
      <template slot-scope="scope">
        <el-button @click="showReport(scope.row.$index, scope.row.report_url)" type="primary" size="small">查看</el-button>
      </template>
    </el-table-column>
  </el-table>
</template>

<script>
// import { fetchList } from '@/api/transaction'
// import API2 from '../../../api/api_autoreport'

export default {
  props:['data_list'],
  data() {
    return {
      datalist: this.data_list
    }
  },
  watch: {
      data_list(val) {
          this.datalist = val;//新增data_list的watch，监听变更并同步到datalist上
      }
  },
  methods: {
    //状态显示转换
    formatStatus: function (is_pass) {
      return is_pass == 1 ? '成功' : is_pass == 0 ? '失败' : '未知';
    },
    //显示报告详情
    showReport: function(index, report_url){
        let that = this;
        // window.location.href = report_url;
        window.open(report_url, "_blank");
        console.log(report_url)
    }
  }
}
</script>

<style>
.demo-table-expand label {
  font-weight: bold;
}
</style>
