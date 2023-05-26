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
      <el-table-column label="Title"  width="120">
        <template slot-scope="scope">
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="Author" width="120" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.author }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Stemfrom" width="120" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.stemfrom }}</span>
        </template>
      </el-table-column>
      <el-table-column label="Cover" width="150" align="center">
        <template slot-scope="scope">
          <el-image :src="scope.row.cover"></el-image>
        </template>
      </el-table-column>
      <el-table-column label="Type" width="120" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.type }}</span>
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

    <!--    分页按钮-->
    <el-pagination
      background
      layout="prev,pager,next"
      :total="count"
      :page-size="pageSize"
      :current-page="page"
      style="margin-top: 20px"
      @current-change="changePage"
    />
  </div>
</template>

<script>

export default {
  data() {
    return {
      list: [],
      listLoading: true,
      page: 1,
      pageSize: 0,
      count: 0
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    // 获取数据
    fetchData() {
      this.listLoading = false
      this.$http({
        path: '/admin/fraud/findAll',
        method: 'get',
        params: {
          page: this.page, // 传参，目前是第几页
          size: 7
        }
      }).then(res => {
        console.log(res)
        this.list = res.data.result
        this.page = res.data.page
        this.pageSize = res.data.pageSize
        this.count = res.data.count
        this.$message({
          message: res.data.msg,
          type: res.data.code === 200 ? 'success' : 'error'
        })
      })
    },
    changePage(page) {
      this.page = page
      this.fetchData()
    },
    update(row) {
      this.$router.push({
        path: '/example/caseEdit',
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
          path: '/admin/fraud/del',
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
