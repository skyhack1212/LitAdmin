<template>
  <el-row class="warp">
    <el-col :span="24" class="warp-breadcrum">
      <el-breadcrumb separator="/">
        <el-breadcrumb-item :to="{ path: '/' }"><span class="fa fa-home"><b>  首页</b></span></el-breadcrumb-item>
        <el-breadcrumb-item>质量管理</el-breadcrumb-item>
        <el-breadcrumb-item>质量列表</el-breadcrumb-item>
      </el-breadcrumb>
    </el-col>

    <el-col :span="24" class="warp-main" v-loading="loading" element-loading-text="拼命加载中">
      <!--工具条-->
      <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
        <el-form :inline="true" :model="filters">
          <el-form-item>
            <el-input v-model="filters.project_id" placeholder="项目ID/服务名" @keyup.enter.native="handleSearch"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" v-on:click="handleSearch">查询</el-button>
          </el-form-item>
          <!-- <el-form-item>
            <el-button type="primary" @click="showAddDialog">新增</el-button>
          </el-form-item> -->
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
      <el-table :data="data_list" highlight-current-row @selection-change="selsChange"
                style="width: 100%;">
        <el-table-column type="selection" width="55"></el-table-column>
        <!-- <el-table-column type="index" width="60"></el-table-column> -->
        <el-table-column prop="report_id" label="报告ID" width="100" sortable></el-table-column>
        <el-table-column prop="project_id" label="项目ID" width="110" sortable></el-table-column>
        <el-table-column prop="service_name" label="服务名" sortable></el-table-column>
        <el-table-column prop="author" label="QA" width="100" sortable></el-table-column>
        <el-table-column prop="create_time" label="执行时间"  min-width="80" sortable></el-table-column>
        <!-- <el-table-column prop="report_url" label="报告详情" sortable></el-table-column> -->
        <el-table-column prop="pass_case" label="通过case数" width="120" sortable></el-table-column>
        <el-table-column prop="fail_case" label="失败case数" width="120" sortable></el-table-column>
        <el-table-column prop="passing_rate" label="case通过率" width="120" sortable></el-table-column>
        <el-table-column prop="is_pass" label="执行状态" width="100" :formatter="formatStatus" sortable>
          <template slot-scope="scope">
            <el-tag v-if="scope.row.is_pass == 1" type="success">
                {{formatStatus(scope.row, scope.column)}}
            </el-tag>
            <el-tag v-else type="danger">
                {{formatStatus(scope.row, scope.column)}}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column type="expand">
          <template>
             <div>
              <v-html-panel :url.asyc="url1">123</v-html-panel>
             </div>
          </template>
          <template slot-scope="scope">
            <!-- <v-html-panel :url.asyc="report_url"></v-html-panel> -->
            <el-form label-position="left" inline class="demo-table-expand">
              <el-form-item label="[失败原因]" label-position="center" >
                  <!-- <v-html-panel :url.asyc="props.row.report_url"></v-html-panel> -->
                <!-- <span>{{ props.row.report_url }}</span> -->
                {{scope.row.reason}}
              </el-form-item>
            </el-form>
          </template>
        </el-table-column>
        <el-table-column prop="reason" label="失败原因" sortable>
          <template slot-scope="scope">
            <el-tag v-if="scope.row.is_pass == 1" type="success" >
                {{scope.row.reason}}
            </el-tag>
            <el-tag v-else type="danger">
                {{scope.row.reason}}
            </el-tag>
          </template>
        </el-table-column>
        <el-table-column label="操作" width="150">
          <template prop="report_url" slot-scope="scope">
            <!-- <a href="http://echarts.baidu.com/examples.html" target="_blank"> -->
            <el-button @click="showReport(scope.row.$index, scope.row.report_url)" type="primary" size="small">查看</el-button>
            <el-button size="small" @click="showEditDialog(scope.row.$index, scope.row)">编辑</el-button>
            <!-- <el-button type="danger" @click="delBook(scope.$index,scope.row)" size="small">删除</el-button> -->
          </template>
        </el-table-column>
      </el-table>

      <el-dialog title="编辑" :visible.sync ="editFormVisible" :close-on-click-modal="false">
        <el-form :model="editForm" label-width="100px" :rules="editFormRules" ref="editForm">
          <el-form-item label="项目ID" prop="project_id">
            <el-input v-model="editForm.project_id" auto-complete="off" readonly></el-input>
          </el-form-item>
          <el-form-item label="服务名" prop="service_name">
            <el-input v-model="editForm.service_name" auto-complete="off" readonly></el-input>
          </el-form-item>
          <el-form-item label="QA" prop="author">
            <el-input v-model="editForm.author" placeholder="qa" auto-complete="off"></el-input>
          </el-form-item>
          <!-- <el-form-item label="执行时间"> -->
            <!-- <el-date-picker type="date" placeholder="选择日期" v-model="editForm.publishAt" disable></el-date-picker> -->
            <!-- <el-date-picker type="date" placeholder="选择日期" v-model="editForm.create_time" disable></el-date-picker> -->
          <!-- </el-form-item> -->
          <el-form-item label="失败原因" prop="reason">
            <el-input type="textarea" v-model="editForm.reason" :rows="8"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click.native="editFormVisible = false">取消</el-button>
          <el-button type="primary" @click.native="editSubmit">提交</el-button>
        </div>
      </el-dialog>

      <!--新增界面-->
      <!-- <el-dialog title="新增" :visible.sync ="addFormVisible" :close-on-click-modal="false">
        <el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm">
          <el-form-item label="服务名" prop="name">
            <el-input v-model="addForm.name" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="作者" prop="author">
            <el-input v-model="addForm.author" auto-complete="off"></el-input>
          </el-form-item>
          <el-form-item label="出版日期">
            <el-date-picker type="date" placeholder="选择日期" v-model="addForm.publishAt"></el-date-picker>
          </el-form-item>
          <el-form-item label="简介" prop="description">
            <el-input type="textarea" v-model="addForm.description" :rows="8"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click.native="addFormVisible = false">取消</el-button>
          <el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
        </div>
      </el-dialog> -->

    </el-col>
  </el-row>
</template>
<script>
  import util from '../../common/util'
  import API2 from '../../api/api_autoreport'
  import HtmlPanel from '../HtmlPanel'

  export default{
    components:{
      'v-html-panel':{
        HtmlPanel
      }
    },
    data(){
      return {
        filters: {
          project_id: 0
        },
        reports: [],
        total: 0,
        page: 1,
        limit: 10,
        loading: false,
        sels: [], //列表选中列
        // url1: '',
        //编辑相关数据
        editFormVisible: false,//编辑界面是否显示
        editFormRules: {
          name: [
            {required: true, message: '请输入书名', trigger: 'blur'}
          ],
          author: [
            {required: false, message: '请输入作者', trigger: 'blur'}
          ],
          description: [
            {required: true, message: '请输入简介', trigger: 'blur'}
          ]
        },
        editForm: {
          report_id: 0,
          service_name: '',
          author: '',
          // publishAt: '',
          create_time: '',
          // description: ''
          reason: ''
        }
      }
    },
    methods: {
      //状态显示转换
      formatStatus: function (row, column) {
        return row.is_pass == 1 ? '成功' : row.is_pass == 0 ? '失败' : '未知';
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
          project_id: that.filters.project_id,
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
      },
      //删除
      // delBook: function (index, row) {
      //   let that = this;
      //   this.$confirm('确认删除该记录吗?', '提示', {type: 'warning'}).then(() => {
      //     that.loading = true;
      //     API.remove(row.id).then(function (result) {
      //       that.loading = false;
      //       if (result && parseInt(result.errcode) === 0) {
      //         that.$message.success({showClose: true, message: '删除成功', duration: 1500});
      //         that.search();
      //       }
      //     }, function (err) {
      //       that.loading = false;
      //       that.$message.error({showClose: true, message: err.toString(), duration: 2000});
      //     }).catch(function (error) {
      //       that.loading = false;
      //       console.log(error);
      //       that.$message.error({showClose: true, message: '请求出现异常', duration: 2000});
      //     });
      //   }).catch(() => {
      //   });
      // },
      //显示编辑界面
      showEditDialog: function (index, row) {
        this.editFormVisible = true;
        this.editForm = Object.assign({}, row);
      },
      //编辑
      editSubmit: function () {
        let that = this;
        this.$refs.editForm.validate((valid) => {
          if (valid) {
            this.loading = true;
            // let para = Object.assign({}, this.editForm);
            let row_tmp = { "report_id" : this.editForm.report_id, "reason" : this.editForm.reason };
            let para = Object.assign({}, row_tmp);
            console.log(para);
            // para.publishAt = (!para.publishAt || para.publishAt == '') ? '' : util.formatDate.format(new Date(para.publishAt), 'yyyy-MM-dd');
            // para.create_time = (!para.create_time || para.create_time == '') ? '' : util.formatDate.format(new Date(para.create_time), 'yyyy-MM-dd HH:mm:DD');
            API2.update(para.report_id, para).then(function (result) {
              that.loading = false;
              console.log(result);
              if (result && parseInt(result.errno) === 0) {
                that.$message.success({showClose: true, message: '修改成功', duration: 2000});
                that.$refs['editForm'].resetFields();
                that.editFormVisible = false;
                that.search();
              } else {
                that.$message.error({showClose: true, message: '修改失败', duration: 2000});
              }
            }, function (err) {
              that.loading = false;
              that.$message.error({showClose: true, message: err.toString(), duration: 2000});
            }).catch(function (error) {
              that.loading = false;
              console.log(error);
              that.$message.error({showClose: true, message: '请求出现异常', duration: 2000});
            });
          }
        });
      },
      // showAddDialog: function () {
      //   this.addFormVisible = true;
      //   this.addForm = {
      //     name: '',
      //     author: '',
      //     publishAt: '',
      //     description: ''
      //   };
      // },
      //新增
      // addSubmit: function () {
      //   let that = this;
      //   this.$refs.addForm.validate((valid) => {
      //     if (valid) {
      //       that.loading = true;
      //       let para = Object.assign({}, this.addForm);
      //       para.publishAt = (!para.publishAt || para.publishAt === '') ? '' : util.formatDate.format(new Date(para.publishAt), 'yyyy-MM-dd');
      //       API.add(para).then(function (result) {
      //         that.loading = false;
      //         if (result && parseInt(result.errcode) === 0) {
      //           that.$message.success({showClose: true, message: '新增成功', duration: 2000});
      //           that.$refs['addForm'].resetFields();
      //           that.addFormVisible = false;
      //           that.search();
      //         } else {
      //           that.$message.error({showClose: true, message: '修改失败', duration: 2000});
      //         }
      //       }, function (err) {
      //         that.loading = false;
      //         that.$message.error({showClose: true, message: err.toString(), duration: 2000});
      //       }).catch(function (error) {
      //         that.loading = false;
      //         console.log(error);
      //         that.$message.error({showClose: true, message: '请求出现异常', duration: 2000});
      //       });

      //     }
      //   });
      // },
      //批量删除
      // batchDeleteBook: function () {
      //   let ids = this.sels.map(item => item.id).toString();
      //   let that = this;
      //   this.$confirm('确认删除选中记录吗？', '提示', {
      //     type: 'warning'
      //   }).then(() => {
      //     that.loading = true;
      //     API.removeBatch(ids).then(function (result) {
      //       that.loading = false;
      //       if (result && parseInt(result.errcode) === 0) {
      //         that.$message.success({showClose: true, message: '删除成功', duration: 1500});
      //         that.search();
      //       }
      //     }, function (err) {
      //       that.loading = false;
      //       that.$message.error({showClose: true, message: err.toString(), duration: 2000});
      //     }).catch(function (error) {
      //       that.loading = false;
      //       console.log(error);
      //       that.$message.error({showClose: true, message: '请求出现异常', duration: 2000});
      //     });
      //   }).catch(() => {

      //   });
      // }
    },
    mounted() {
      this.handleSearch();
      this.url1='http://10.xxx.xx.152/rtcharge_ci/rtcharge_xuri_1532597239.html';
      console.log(this.url1)
    }
  }
</script>

<style>
  .demo-table-expand label {
    font-weight: bold;
  }
</style>
