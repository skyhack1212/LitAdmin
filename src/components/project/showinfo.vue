<template>
  <div class="page-wrapper" v-loading="loading" element-loading-text="数据请求中">
    <!--导航区-->
    <div class="page-breadcrumb">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/project/list' }">项目管理</el-breadcrumb-item>
        <el-breadcrumb-item :to="{ path: '/project/list' }">项目列表</el-breadcrumb-item>
        <el-breadcrumb-item>项目详情</el-breadcrumb-item>
      </el-breadcrumb>
      <!--返回按钮-->
      <!-- <div class="button-back">
        <router-link :to="{ path:'/project/list'}">
          <el-button type="default" size="mini">返回</el-button>
        </router-link>
      </div> -->
    </div>

    <!--主内容区-->
    <div class="page-main" >
      <el-row :gutter="8">
        <el-col :span="7" style="line-height: 40px;">
          <h3>【{{ data_list[0].project_name }}】项目质量报告</h3>
        </el-col>
        <el-col :span="15" style="line-height: 70.81px;">
          <!-- <el-switch @change="changeSwitch" on-value="1" active-text="测试通过" off-value="0" inactive-text="测试中" v-model="value2"/> -->
          <el-switch
              v-model="value2"
              active-color="#00A854"
              active-text="测试通过"
              active-value="100"
              inactive-color="#F04134"
              inactive-text="测试中"
              inactive-value="0"
              @change="changeSwitch(value2)">
            </el-switch>
        </el-col>
        <el-col :span="1.5" align="center" style="line-height: 70.81px;">
            <!--返回按钮-->
            <router-link :to="{ path:'/project/list'}">
              <el-button type="info" class="btn-long">返回</el-button>
            </router-link>
        </el-col>
      </el-row>
      <hr style="height:10px;border:none;border-top:10px groove skyblue;" />
      <el-row :gutter="8">
        <el-col :xs="{span: 24}" :sm="{span: 24}" :md="{span: 24}" :lg="{span: 12}" :xl="{span: 12}" style="padding-right:8px;margin-bottom:30px;">
          <el-col :span="8">
            <div class="grid-content bg-purple">
              开始时间：{{ data_list_project[0].project_start_time }}
            </div>
          </el-col>
          <el-col :span="8">
            <div class="grid-content bg-purple-light">
              提测时间：{{ data_list_project[0].project_post_test_time }}
            </div>
          </el-col>
          <br></br>
          <el-col :span="8">
            <div class="grid-content bg-purple">
              测试组：baseqa
            </div>
          </el-col>
          <el-col :span="8">
            <div class="grid-content bg-purple-light">
              产品线：计费
            </div>
          </el-col>
          <br></br>
          <el-col :span="8">
            <div class="grid-content bg-purple">
              当前状态：
            </div>
          </el-col>
          <el-col :span="8">
            <div class="grid-content bg-purple-light">
              项目类型：产品需求
            </div>
          </el-col>
        </el-col>
        <el-col :xs="{span: 30}" :sm="{span: 30}" :md="{span: 30}" :lg="{span: 16}" :xl="{span: 12}">
          <!-- PanelGroup -->
          <panel-group @handleSetLineChartData="handleSetLineChartData"></panel-group>
        </el-col>
      </el-row>

      <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
        <line-chart :chart-data="lineChartData"></line-chart>
      </el-row>

      <el-row :gutter="8">
        <el-col :xs="{span: 6}" :sm="{span: 6}" :md="{span: 6}" :lg="{span: 3}" :xl="{span: 3}">
          <!-- <pan-thumb :image="image_dev">开发</pan-thumb> -->
          <pan-thumb style="float: left" :image="image_dev"> Roles:
            <span class="pan-info-roles" :key='item' v-for="item in roles_rd">{{item}}</span>
          </pan-thumb>
        </el-col>
        <el-col :xs="{span: 24}" :sm="{span: 24}" :md="{span: 24}" :lg="{span: 12}" :xl="{span: 12}" style="padding-right:8px;margin-bottom:30px;">
          <transaction-table></transaction-table>
        </el-col>
        <!-- <el-col :xs="{span: 12}" :sm="{span: 12}" :md="{span: 12}" :lg="{span: 6}" :xl="{span: 5}">
          <todo-list></todo-list>
        </el-col> -->
        <el-col :xs="{span: 12}" :sm="{span: 12}" :md="{span: 12}" :lg="{span: 6}" :xl="{span: 5}">
          <box-card></box-card>
        </el-col>
      </el-row>
      <el-row :gutter="8">
        <el-col :xs="{span: 6}" :sm="{span: 6}" :md="{span: 6}" :lg="{span: 3}" :xl="{span: 3}">
          <pan-thumb style="float: left" :image="image_qa"> Roles:
            <span class="pan-info-roles" :key='item' v-for="item in roles_qa">{{item}}</span>
          </pan-thumb>
        </el-col>
        <el-col :xs="{span: 24}" :sm="{span: 24}" :md="{span: 24}" :lg="{span: 12}" :xl="{span: 12}" style="padding-right:8px;margin-bottom:30px;">
          <transaction-table></transaction-table>
        </el-col>
      </el-row>

      <el-row :gutter="32">

        <!-- <el-col :xs="24" :sm="24" :lg="8">
          <div class="chart-wrapper">
            <raddar-chart></raddar-chart>
          </div>
        </el-col> -->
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
      </el-row>



      <hr style="height:10px;border:none;border-top:10px groove skyblue;" />


      <el-row :gutter="8">
        <el-col :span="7" style="line-height: 40px;">
          <h3>【{{ data_list[0].project_name }}】项目质量报告</h3>
        </el-col>
        <el-col :span="15" style="line-height: 70.81px;">
          <!-- <el-switch @change="changeSwitch" on-value="1" active-text="测试通过" off-value="0" inactive-text="测试中" v-model="value2"/> -->
          <el-switch
              v-model="value2"
              active-color="#00A854"
              active-text="测试通过"
              active-value="100"
              inactive-color="#F04134"
              inactive-text="测试中"
              inactive-value="0"
              @change="changeSwitch(value2)">
            </el-switch>
        </el-col>
        <el-col :span="1.5" align="center" style="line-height: 70.81px;">
            <!--返回按钮-->
            <router-link :to="{ path:'/project/list' }">
              <el-button type="info" class="btn-long">返回</el-button>
            </router-link>
        </el-col>
      </el-row>
    </div>
  </div>
</template>
<script>
  import API2 from '../../api/api_project'
  import echarts from 'echarts'
  import PanelGroup from './group/PanelGroup'
  import LineChart from './group/LineChart'
  import RaddarChart from './group/RaddarChart'
  import PieChart from './group/PieChart'
  import BarChart from './group/BarChart'
  import TransactionTable from './group/TransactionTable'
  import TodoList from './group/TodoList'
  import BoxCard from './group/BoxCard'
  import PanThumb from '../PanThumb'

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
      LineChart,
      RaddarChart,
      PieChart,
      BarChart,
      TransactionTable,
      TodoList,
      BoxCard,
      PanThumb
    },
    created() {
      this.project_id = this.$route.query.project_id;//获取上个页面传递的id,在下面获取数据的时候先提交id
      this.project_name = this.$route.query.project_name;//同上类似
      // console.log(this.$route.query);
      // console.log(this.project_name);
    },
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
        data_list: [],
        data_list_project: [],
        loading: false,
        lineChartData: lineChartData.newVisitis,
        roles_rd: {'rd': '刘伟杰'},
        roles_qa: {'qa': '王杰'},
        image_dev: 'https://wpimg.wallstcn.com/577965b9-bb9e-4e02-9f0c-095b41417191',
        image_qa: 'https://oa.xxxxx-inc.com/moa/sylla/mapi/img?id=wangjie214481&s=1&w=200&h=200'
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
        this.search_project();
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
      },

      //获取项目列表
      search_project() {
        let that = this;
        let params_project = {
          page_num: that.page,
          page_limit: 10,
          // name: that.filters.name
          project_id_list: [that.project_id]
        };

        that.loading = true;
        API2.findProjectList(params_project).then(function (result) {
          that.loading = false;
          if (result && result.data_list) {
            that.total = result.total;
            that.data_list_project = result.data_list;
          }
          console.log(that.data_list_project)
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
</style>
