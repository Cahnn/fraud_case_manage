<template>
  <div class="app-container">
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
    >
      <el-table-column align="center" label="ID">
        <template slot-scope="scope">
          {{ scope.$index + 1 }}
        </template>
      </el-table-column>
      <el-table-column label="UserName" align="center">
        <template slot-scope="scope">
          {{ scope.row.username }}
        </template>
      </el-table-column>
      <el-table-column label="Password" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.password }}</span>
        </template>
      </el-table-column>
<!--      <el-table-column class-name="status-col" label="Status" align="center">-->
<!--        <template slot-scope="scope">-->
<!--          <el-tag :type="scope.row.status | statusFilter">{{ scope.row.status }}</el-tag>-->
<!--        </template>-->
<!--      </el-table-column>-->
      <el-table-column align="center" prop="created_at" label="Operation" >
        <template slot-scope="scope">
          <i class="el-icon-time" />
          <span>{{ scope.row.display_time }}</span>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>

export default {
  data() {
    return {
      listLoading: false,
      list: []
    }
  },
  mounted() {
    // this.$axios.get('http://localhost:3001/users/find').then(res => {
    //   console.log(res.data.result)
    //   this.list = res.data.result
    // }).catch(err => {
    //   console.log('请求异常', err)
    // })
  },
  created() {
    // this.fetchData()
    this.$http({
      path: 'https://cnodejs.org/api/v1/topics',
      method: 'get',
      params: {

      }
    }).then(res => {
      console.log(res)
    })
  }
  // methods: {
  //   fetchData() {
  //     this.listLoading = true
  //   }
  // }
}
</script>

<style scoped>

</style>
