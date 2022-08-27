<template>
  <div class="app-container">
    <el-card class="filter-container" shadow="never">
      <div>
        <i class="el-icon-search"></i>
        <span>筛选搜索</span>
        <el-button style="float: right" type="primary" @click="handleSearchList()"
                   size="small">查询搜索</el-button>
        <el-button style="float: right;margin-left: 30px" @click="handleResetSearch()"
                   size="small">重置</el-button>
      </div>
      <div style="margin-top: 15px">
        <el-form :inline="true" :model="listQuery" size="small" label-width="140px">
          <el-form-item label="输入搜索：">
            <el-input v-model="listQuery.keyword" class="input-width" placeholder="帐号/姓名" clearable></el-input>
          </el-form-item>
        </el-form>
      </div>
    </el-card>

    <el-card class="operate-container" shadow="never">
      <i class="el-icon-tickets"></i>
      <span>数据列表</span>
      <el-button size="mini" class="btn-add" @click="handleAdd()" style="margin-left: 20px">添加</el-button>
    </el-card>

    <div class="table-container">
      <el-table ref="adminTable" :data="list" style="width: 100%;" v-loading="listLoading" border>
        <el-table-column label="编号" width="100" align="center">
          <template slot-scope="scope">{{scope.row.id}}</template>
        </el-table-column>

        <el-table-column label="账号" align="center">
          <template slot-scope="scope">{{scope.row.username}}</template>
        </el-table-column>

        <el-table-column label="姓名" align="center">
          <template slot-scope="scope">{{scope.row.nickName}}</template>
        </el-table-column>

        <el-table-column label="邮箱" align="center">
          <template slot-scope="scope">{{scope.row.email}}</template>
        </el-table-column>

        <el-table-column label="添加时间" width="160" align="center">
          <template slot-scope="scope">{{scope.row.createTime | formatDateTime}}</template>
        </el-table-column>

        <el-table-column label="是否启用" width="140" align="center">
          <template slot-scope="scope">
            <el-switch
              @change="handleStatusChange(scope.$index,scope.row)"
              :active-value="1"
              :inactive-value="0"
              v-model="scope.row.status"
            >
            </el-switch>
          </template>
        </el-table-column>

        <el-table-column label="操作" width="180" align="center">
          <template slot-scope="scope">
            <el-button size="mini" type="text" @click="handleSelectRole(scope.$index,scope.row)">分配角色</el-button>
            <el-button size="mini" type="text" @click="handleUpdate(scope.$index,scope.row)">编辑</el-button>
            <el-button size="mini" type="text" @click="handleDelete(scope.$index,scope.row)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <div class="pagination-container">
      <el-pagination
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        layout="total,sizes,prev,pager,next,jumper"
        :current-page.sync="listQuery.pageNum"
        :page-size="listQuery.pageSize"
        :page-sizes="[10,15,20]"
        :total="total">
        ></el-pagination>
    </div>

    <el-dialog :title="isEdit?'编辑用户':'添加用户'" :visible.sync="dialogVisible" width="40%">

      <el-form :model="admin" ref="adminForm" label-width="150px" size="small">

        <el-form-item label="账号:">
          <el-input v-model="admin.username" style="width: 250px"></el-input>
        </el-form-item>

        <el-form-item label="姓名:">
          <el-input v-model="admin.nickName" style="width: 250px"></el-input>
        </el-form-item>

        <el-form-item label="邮箱:">
          <el-input v-model="admin.email" style="width: 250px"></el-input>
        </el-form-item>

        <el-form-item label="密码:">
          <el-input v-model="admin.password" type="password" style="width: 250px"></el-input>
        </el-form-item>

        <el-form-item label="备注:">
          <el-input v-model="admin.note" type="textarea" :rows="5" style="width: 250px"></el-input>
        </el-form-item>

        <el-form-item label="是否启用">
          <el-radio-group v-model="admin.status">
            <el-radio :label="1">是</el-radio>
            <el-radio :label="0">否</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
          <el-button @click="dialogVisible = false" size="small">取 消</el-button>
          <el-button type="primary" @click="handleDialogConfirm()" size="small">确 定</el-button>
        </span>
    </el-dialog>

  </div>
</template>
<script>

  import {formatDate} from "@/utils/date";
  import {fetchList,createAdmin,updateAdmin,updateStatus,deleteAdmin,getRoleByAdmin,allocRole} from "@/api/login"

  const defaultListQuery = {
    pageNum: 1,
    pageSize: 10,
    keyword: null
  };

  const defaultAdmin = {
    id: null,
    username: null,
    password: null,
    nickName: null,
    email: null,
    note:null,
    status:1
  }

  export default{
    data(){
      return {
        listQuery: Object.assign({},defaultListQuery),
        list: null,
        total: null,
        listLoading: false,
        isEdit: false,
        dialogVisible: false,
        admin: Object.assign({},defaultAdmin)
      }
    },
    created() {
      this.getList();
    },
    filters: {
      formatDateTime(time){
        if (time == null || time === ''){
          return 'N/A';
        }

        let date = new Date(time);
        return formatDate(date ,'yyyy-MM-dd hh:mm:ss')

      }
    },
    methods:{
      handleSearchList(){

      },
      handleResetSearch(){

      },
      handleAdd(){
        this.dialogVisible = true;
        this.isEdit = false;
        this.admin = Object.assign({},defaultAdmin);

      },
      handleSelectRole(index,row){

      },
      handleUpdate(index,row){
        this.dialogVisible = true;
        this.isEdit = true;
        this.admin = Object.assign({},row);
      },
      handleDelete(index,row){
        this.$confirm('是否要删除该用户？','提示',{
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(()=>{
          deleteAdmin(row.id).then(resonse =>{
            this.$message({
              type: 'success',
              message: '删除成功！'
            });
            this.getList();
          })
        })
      },
      handleSizeChange(val){

      },
      handleCurrentChange(val){

      },
      getList(){
        this.listLoading = true;
        fetchList(this.listQuery).then(respose =>{
          this.listLoading = false;
          this.list = respose.data.list;
          this.total = respose.data.total;
        });
      },
      handleDialogConfirm(){
        this.$confirm('是否要确认？','提示',{
          confirmButtonText: '确认',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(()=>{

          if (this.isEdit){
            updateAdmin(this.admin.id,this.admin).then(response =>{
              this.$message({
                message: '修改成功！',
                type: 'success'
              });
              this.dialogVisible = false;
              this.getList();
            })
          }

          createAdmin(this.admin).then(response =>{
            this.$message({
              message: '添加成功!',
              type: 'success'
            });
            this.dialogVisible = false;
            this.getList();
          })
        })

      }



    }
  }

</script>
<style>

</style>
