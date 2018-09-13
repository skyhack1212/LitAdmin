<template>
  <div class="page-wrapper" v-loading="loading" element-loading-text="数据请求中">
    <!--导航区-->
    <div class="page-breadcrumb">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/project/list' }">项目管理</el-breadcrumb-item>
        <el-breadcrumb-item :to="{ path: '/project/list' }">项目列表</el-breadcrumb-item>
        <el-breadcrumb-item :to="{ path: '/project/show/'+ project_id }">项目详情</el-breadcrumb-item>
        <el-breadcrumb-item>报告详情</el-breadcrumb-item>
      </el-breadcrumb>
      <!--返回按钮-->
      <!-- <div class="button-back">
        <router-link :to="{ path:'/project/list'}">
          <el-button type="default" size="mini">返回</el-button>
        </router-link>
      </div> -->
    </div>

    <!--主内容区-->
    <div class="page-main">
      <el-row :gutter="8">
        <el-col :span="22">
          <h3>【{{ project_name }}】各模块分布图汇总</h3>
        </el-col>
        <el-col :span="1.5" align="center" style="line-height: 70.81px;">
            <!--返回按钮-->
            <router-link :to="{ path:'/project/show/'+project_id}">
              <el-button type="info" class="btn-long">返回</el-button>
            </router-link>
        </el-col>
      </el-row>
      <hr style="height:10px;border:none;border-top:10px groove skyblue;" />
      <el-row :gutter="8">
        <el-col :xs="24" :sm="24" :lg="8">
          <div class="chart-wrapper">
            <pie-chart></pie-chart>
          </div>
        </el-col>
        <!-- <el-col :xs="24" :sm="24" :lg="8">
          <div class="chart-wrapper">
            <raddar-chart></raddar-chart>
          </div>
        </el-col> -->

        <el-col :xs="24" :sm="24" :lg="8">
          <div class="chart-wrapper">
            <bar-chart></bar-chart>
          </div>
        </el-col>
      </el-row>
      <el-row :gutter="8">
        <el-col :span="22" style="line-height: 40px;">
          <h3>【{{ project_name }}】功能测试报告列表</h3>
        </el-col>

      </el-row>
      <hr style="height:10px;border:none;border-top:10px groove skyblue;" />

      <el-row :gutter="8">
        <el-col :span="24" class="toolbar">
          <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="10" :total="total"
                         style="float:right;">
          </el-pagination>
        </el-col>
        <el-col>
          <Transactionreportlist-table v-bind:data_list="data_list"></Transactionreportlist-table>
        </el-col>
      </el-row>


    <hr style="height:10px;border:none;border-top:10px groove skyblue;" />


      <el-row :gutter="8">
        <el-col :span="22" style="line-height: 40px;">
          <h3>【{{ project_name }}】功能测试报告列表</h3>
        </el-col>
        <el-col :span="1.5" align="center" style="line-height: 70.81px;">
            <!--返回按钮-->
            <router-link :to="{ path:'/project/show/' + project_id}">
              <el-button type="info" class="btn-long">返回</el-button>
            </router-link>
        </el-col>
      </el-row>
    </div>
  </div>
</template>
<script>
  import API2 from '../../../api/api_autoreport'
  import echarts from 'echarts'
  import PanelGroup from '../group/PanelGroup'
  // import LineChart from '../group/LineChart'
  import RaddarChart from '../group/RaddarChart'
  import PieChart from '../group/PieChart'
  import BarChart from '../group/BarChart'
  import TransactionreportlistTable from './TransactionreportlistTable'
  import TodoList from '../group/TodoList'
  import BoxCard from '../group/BoxCard'
  import PanThumb from '../../PanThumb'

  const lineChartData = {
    newVisitis: {
      expectedData: [100, 120, 161, 134, 105, 160, 165],
      actualData: [120, 82, 91, 154, 162, 140, 145]
    },
    messages: {
      expectedData: [200, 192, 120, 144, 160, 130, 140],
      actualData: [180, 160, 151, 106, 145, 150, 130]
    },
    purchases: {
      expectedData: [80, 100, 121, 104, 105, 90, 100],
      actualData: [120, 90, 100, 138, 142, 130, 130]
    },
    shoppings: {
      expectedData: [130, 140, 141, 142, 145, 150, 160],
      actualData: [120, 82, 91, 154, 162, 140, 130]
    }
  }

  export default {
    components: {
      PanelGroup,
      // LineChart,
      RaddarChart,
      PieChart,
      BarChart,
      TransactionreportlistTable,
      TodoList,
      BoxCard,
      PanThumb
    },
    data() {
      return {
        value2: true,
        loading: true,
        data_list: [],
        // result: { data_list: [], errno:0 },
        total: 0,
        page: 1,
        limit: 10
      }
    },
    created() {
      this.project_id = this.$route.query.project_id;//获取上个页面传递的id,在下面获取数据的时候先提交id
      this.project_name = this.$route.query.project_name;//同上类似
      // console.log(this.$route)
    },
    methods: {
      //状态显示转换
      formatStatus: function (row, column) {
        return row.is_pass == 1 ? '成功' : row.is_pass == 0 ? '失败' : '未知';
      },
      handleCurrentChange(val) {
        this.page = val;
        console.log(this.page)
        this.search();
      },
      handleSearch(){
        this.total = 0;
        this.page = 1;
        this.search();
      },
      search(){
        let that = this;
        // let params = {
        //   page: that.page,
        //   limit: 10,
        //   name: that.filters.name
        // };
        let params = {
          page_num: that.page,
          page_limit: 10,
          project_id: that.project_id,
          service_name: '',
          start_time: '',
          end_time: ''
        };

        that.loading = true;
        API2.findList(params).then(function (result) {
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
      },
      selsChange: function (sels) {
        this.sels = sels;
        console.log(this.sels)
      },
      //显示报告详情
      showReport: function(index, report_url){
          let that = this;
          // window.location.href = report_url;
          window.open(report_url, "_blank");
          console.log(report_url)
      }

    },
    mounted() {
      this.handleSearch();
      // this.url1='http://10.xxx.xx.152/rtcharge_ci/rtcharge_xuri_1532597239.html';
      // console.log(this.url1)
    }
  }
</script>

<style>
  .demo-table-expand label {
    font-weight: bold;
  }
</style>
