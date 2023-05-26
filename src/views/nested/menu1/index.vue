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
      <el-table-column align="center" label="ID" width="95">
        <template slot-scope="scope">
          {{ scope.$index+1 }}
        </template>
      </el-table-column>
      <el-table-column label="Title"  width="200">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="Cover" width="200" align="center">
        <template slot-scope="scope">
          <el-image :src="scope.row.cover"></el-image>
        </template>
      </el-table-column>
      <el-table-column label="Content" show-overflow-tooltip>
        <template slot-scope="scope">
          {{ scope.row.content }}
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="Cooperation" width="200">
        <template slot-scope="scope">
          <el-button type="primary" @click="update(scope.row)">修改</el-button>
          <el-button @click="del(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

  </div>
</template>

<script>

export default {
  data() {
    return {
      list: [],
      listLoading: true
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      this.listLoading = false
      this.$http({
        path: '/admin/activity/findAll',
        method: 'get'
      }).then(res => {
        console.log('所有内容', res)
        this.list = res.data.result
        this.$message({
          message: res.data.msg,
          type: res.status === 200 ? 'success' : 'error'
        })
      })
    },
    changePage(page) {
      this.page = page
      this.fetchData()
    },
    update(row) {
      this.$router.push({
        path: '/nested/menu2',
        query: {
          _id: row._id
        }
      })
    },
    del(row) {
      this.$confirm('确定要删除吗？', '提示', {
        confirmButtonText: '删除',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          path: '/admin/activity/del',
          method: 'post',
          params: {
            _id: row._id
          }
        }).then(res => {
          this.$message({
            message: res.data.msg,
            type: res.data.code === 200 ? 'success' : 'error'
          })
          if (res.data.code === 200) {
            this.fetchData()
          }
        })
      }).catch(() => {})
    }
  }
}
</script>

