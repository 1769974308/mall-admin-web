<template>
  <el-table :data="tableData" style="width: 100%">

    <el-table-column label="日期" width="180">
      <template slot-scope="scope">
        <i class="el-icon-time"></i>
        <span style="margin-left: 10px">{{scope.row.date}}</span>
      </template>
    </el-table-column>

    <el-table-column label="姓名" width="180">
      <template slot-scope="scope">
        <el-popover trigger="hover" placement="top">
          <p>姓名:{{scope.row.name}}</p>
          <p>住址:{{scope.row.address}}</p>
          <div slot="reference" class="name-wrapper">
            <el-tag size="medium">{{scope.row.name}}</el-tag>
          </div>
        </el-popover>
      </template>
    </el-table-column>

    <el-table-column label="操作">
      <template slot-scope="scope">
        <el-button size="mini" @click="handleEdit(scope.$index,scope.row)">编辑</el-button>
        <el-button size="mini" @click="handleDelete(scope.$index,scope.row)">删除</el-button>
      </template>
    </el-table-column>

  </el-table>

</template>

<script>

  export default {
    data(){
      return {
        tableData:[
          {
            date: '2022-08-26',
            name: '小虎',
            address: '深圳宝安区西乡街道1号'
          },
          {
            date: '2022-08-27',
            name: '小龙',
            address: '深圳宝安区西乡街道2号'
          }
        ]
      }
    },
    methods:{
      handleEdit(index,row){
        console.log(index,row)
      },
      handleDelete(index,row){
        console.log(index,row)
      }
    }
  }

</script>
<style>

</style>
