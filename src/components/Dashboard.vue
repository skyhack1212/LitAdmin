<template>
  <div class="page-wrapper" v-loading="loading" element-loading-text="数据请求中">
    <!--导航区-->
    <div class="page-breadcrumb">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/dashboard' }"><span class="fa fa-home"><b>  首页</b></span></span></el-breadcrumb-item>
      </el-breadcrumb>
      <!--返回按钮-->
      <!-- <div class="button-back">
        <router-link :to="{ path:'/project/list'}">
          <el-button type="default" size="mini">返回</el-button>
        </router-link>
      </div> -->
    </div>

    <div class="app-container documentation-container">
      <h1>懒人工具箱</h1>
      <hr style="height:10px;border:none;border-top:10px groove skyblue;" />
      <!-- <dropdown-menu style="float:left;margin-left:50px;" title='公共工具集' :items='commonToolList'></dropdown-menu> -->
      <!-- <dropdown-menu style="float:left;margin-left:50px;" title='计费系统工具集' :items='rtchargeToolList'></dropdown-menu> -->
      <!-- <dropdown-menu style="float:left;margin-left:50px;" title='Vue学习与实践' :items='VueStudyList'></dropdown-menu> -->
      <!-- <dropdown-menu style="float:left;margin-left:50px;" title='Docker学习与实践' :items='DockerStudyList'></dropdown-menu> -->
      <!-- <dropdown-menu style="float:left;margin-left:50px;" title='日常常用系统汇总' :items='workSystemList'></dropdown-menu> -->
      <el-row :gutter="20" style="margin-top:50px;">
        <el-col :span="6">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>公共</span>
            </div>
            <div class="component-item" style="height:450px;">
              <dropdown-menu style="margin:0 auto;" title='公共工具集' :items='commonToolList'></dropdown-menu>
            </div>
          </el-card>
        </el-col>
        <el-col :span="6">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>计费</span>
            </div>
            <div class="component-item" style="height:450px;">
              <dropdown-menu style="margin:0 auto;" title='计费系统工具集' :items='rtchargeToolList'></dropdown-menu>
            </div>
          </el-card>
        </el-col>
        <el-col :span="6">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>Vue</span>
            </div>
            <div class="component-item" style="height:450px;">
              <dropdown-menu style="margin:0 auto;" title='Vue学习与实践' :items='VueStudyList'></dropdown-menu>
            </div>
          </el-card>
        </el-col>
        <el-col :span="6">
          <el-card class="box-card">
            <div slot="header" class="clearfix">
              <span>日常</span>
            </div>
            <div class="component-item" style="height:450px;">
              <dropdown-menu style="margin:0 auto;" title='日常常用系统汇总' :items='workSystemList'></dropdown-menu>
            </div>
          </el-card>
        </el-col>
      </el-row>
    </div>

    <!--主内容区-->
    <div class="page-main">
      <el-row :gutter="8">
        <el-col :xs="{span: 60}" :sm="{span: 60}" :md="{span: 60}" :lg="{span: 32}" :xl="{span: 24}">
          <!-- PanelGroup -->
          <panel-group @handleSetLineChartData="handleSetLineChartData"></panel-group>
        </el-col>
      </el-row>

      <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
        <line-chart :chart-data="lineChartData"></line-chart>
      </el-row>

      <el-row :gutter="32">

        <el-col :xs="24" :sm="24" :lg="8">
          <div class="chart-wrapper">
            <raddar-chart></raddar-chart>
          </div>
        </el-col>
        <el-col :xs="24" :sm="24" :lg="8">
          <div class="chart-wrapper">
            <pie-chart></pie-chart>
          </div>
        </el-col>
        <el-col :xs="24" :sm="24" :lg="8">
          <div class="chart-wrapper">
            <bar-chart></bar-chart>
          </div>
        </el-col>
        <el-col :xs="{span: 12}" :sm="{span: 12}" :md="{span: 12}" :lg="{span: 6}" :xl="{span: 5}">
          <todo-list></todo-list>
        </el-col>
      </el-row>


    </div>
  </div>
</template>
<script>
  import API2 from '../api/api_autoreport'
  import echarts from 'echarts'
  import PanelGroup from './DashboardGroup/PanelGroup'
  import LineChart from './DashboardGroup/LineChart'
  import RaddarChart from './DashboardGroup/RaddarChart'
  import PieChart from './DashboardGroup/PieChart'
  import BarChart from './DashboardGroup/BarChart'
  import TransactionTable from './DashboardGroup/TransactionTable'
  import TodoList from './DashboardGroup/TodoList'
  import BoxCard from './DashboardGroup/BoxCard'
  import PanThumb from './PanThumb'
  import DropdownMenu from './Share0/dropdownMenu'

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
    name: 'documentation',
    components: {
      PanelGroup,
      LineChart,
      RaddarChart,
      PieChart,
      BarChart,
      TransactionTable,
      TodoList,
      BoxCard,
      PanThumb,
      DropdownMenu
    },
    // created() {
    //   this.project_id = this.$route.params.project_id;//获取上个页面传递的id,在下面获取数据的时候先提交id
    // },
    data() {
      return {
        value2: true,
        filters: {
          name: ''
        },
        loading: false,
        // result: { data_list: [], errno:0 },
        total: 0,
        page: 1,
        limit: 10,
        loading: false,
        lineChartData: lineChartData.newVisitis,
        roles_rd: {'rd': 'xxxrd'},
        roles_qa: {'qa': 'qaxxx'},
        image_dev: 'https://wpimg.wallstcn.com/577965b9-bb9e-4e02-9f0c-095b41417191',
        image_qa: 'https://oa.xxxxx-inc.com/moa/sylla/mapi/img?id=wangjie214481&s=1&w=200&h=200',
        commonToolList: [
          { title: '0 分库分表计算', href: 'http://xxxxx.qcadmin.xxxxx/fenkufenbiaomod.html' },
          { title: '0 分库分表计算(优化)', href: 'http://xxxxx.qcadmin.xxxxx/fenkufenbiaomod/index.php' },
          { title: '5 qapidocs', href: 'http://xxxxx.qcadmin.xxxxx/qapidoc' },
          { title: '6 supervisor', href: 'http://10.xxx.xx.152:9001' },
          { title: '7 celery flower', href: 'http://10.xxx.xx.152:5555' },
          { title: '8 redis-admin', href: 'http://10.xxx.xx.152:8088/redis-admin/' },
          { title: '9.1 171zk_kafka_web', href: 'http://10.xxx.xx.152:8097' },
          { title: '9.2 88zk_kafka_web', href: 'http://10.xxx.xx.152:8098' },
          { title: '9.3 232zk_kafka_web', href: 'http://10.xxx.xx.152:8099' },
          { title: '10 encode和decode,unix时间转换等', href: 'http://xxxxx.qcadmin.xxxxx/encodeAndDecode/index.php' },
          // { title: '11 字符互转(unicode<->汉字)', href: 'http://xxxxx.qcadmin.xxxxx/reconvert.html' },
          // { title: '12 Jupyter notebook', href: 'http://10.xxx.xx.152:9527/tree' }
        ],
        rtchargeToolList: [
          { title: '0 rtcharge-xuri代码静态分析', href: 'http://xxxxx.qcadmin.xxxxx/rtcharge-xuri_DEMO/html/index.html' },
          { title: '0 charge自动化框架代码静态分析', href: 'http://xxxxx.qcadmin.xxxxx/xxxxx-charge1.1.6_DEMO/html/index.html' },
          { title: '0 唯一号代码静态分析', href: 'http://xxxxx.qcadmin.xxxxx/uniqueid-server_1.0.17-2_DEMO/html/index.html' },
          { title: '1.1 rtcharge自动化报告', href: 'http://10.xxx.xx.152/rtcharge/' },
          { title: '1.2 charge-route自动化报告', href: 'http://10.xxx.xx.152/chargeroute/' },
          { title: '1.3 consume-query自动化报告', href: 'http://10.xxx.xx.152/consumequery/' },
          { title: '1.4 bill-centre自动化报告', href: 'http://10.xxx.xx.152/billcentre/' },
          { title: '1.5 stream-charge自动化报告', href: 'http://10.xxx.xx.152/stream-charge/' },
          // { title: '4.1 实时计费库表一键清理', href: 'http://10.xxx.xx.152/rtcharge/' },
          // { title: '4.2 实时计费日志一键清理', href: 'http://10.xxx.xx.152/rtcharge/' },
          // { title: '4.3 实时计费断点一键重置', href: 'http://10.xxx.xx.152/rtcharge/' },
          // { title: '4.4 实时计费自动化一键执行', href: 'http://10.xxx.xx.152/rtcharge/' },
          { title: '13 唯一号时序图', href: 'http://xxxxx.qcadmin.xxxxx/sequence_id_live.html' }
        ],
        VueStudyList: [
          { title: '0 异常测试平台', href: 'http://10.xxx.xx.152:8088' },
          { title: '1 质量管理平台', href: 'http://10.xxx.xx.152:8088' },
          { title: '2 计费可视化demo', href: 'http://xxxxx.qcadmin.xxxxx/uniqueid-server_1.0.17-2_DEMO/html/index.html' },
          { title: '3 计费日志管理demo', href: '' },
          { title: '3 计费数据管理demo', href: '' },
          { title: '4 代管机器人demo', href: '' },
          { title: '5 emer机器人demo', href: '' },
          { title: '待更新。。。', href: '' }
        ],
        DockerStudyList: [
          { title: '1 功能自动化平台demo', href: 'http://10.xx.xxx.137:5000' },
          { title: '2 shipyard镜像容器管理平台', href: 'http://10.xx.xxx.137:8080' },
          { title: '3--[git_hug]帮你快速学习git命令', href: '' },
          { title: '待更新。。。', href: '' }
        ],
        workSystemList: [
          { title: '8--MINIwiki首页', href: 'http://10.xxx.xx.152:8090' },
          { title: '待更新。。。', href: '' }
        ]
      }
    },
    methods: {
      changeSwitch (data) {
        console.log(data)
      },
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
          project_id: that.project_id
        };

        that.loading = false;
        API2.findTestList(params).then(function (result) {
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
      // demo
      handleSetLineChartData(type) {
        this.lineChartData = lineChartData[type]
      }
    },
    mounted() {
      this.handleSearch();
    }
  }
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
    .dashboard-editor-container {
      padding: 32px;
      background-color: rgb(240, 242, 245);
      .chart-wrapper {
        background: #fff;
        padding: 16px 16px 0;
        margin-bottom: 32px;
      }
    }
    .documentation-container {
      margin: 50px;
      .document-btn {
        float: left;
        margin-left: 50px;
        vertical-align: middle;
        display: block;
        cursor: pointer;
        background: black;
        color: white;
        height: 60px;
        width: 200px;
        line-height: 60px;
        font-size: 20px;
        text-align: center;
      }
    }
</style>
