<template>
  <div class="app-container">
    <el-button type="primary" style="margin-bottom: 30px" @click="addBtn">添加问答</el-button>
    <el-dialog title="添加问答" :visible.sync="dialogFormVisible">
      <el-form :model="issue">
        <el-form-item label="问题" :label-width="formLabelWidth" required>
          <el-input v-model="issue.question" type="text" name="question" />
        </el-form-item>
        <el-form-item label="回答" :label-width="formLabelWidth" required>
          <el-input v-model="issue.answer" type="text" name="answer" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button v-if="!this.isUpdate" type="primary" @click="add">确定添加</el-button>
        <el-button v-else type="primary" @click="update">确定修改</el-button>
      </div>
    </el-dialog>
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
      <el-table-column label="问题" align="center">
        <template slot-scope="scope">
          {{ scope.row.question }}
        </template>
      </el-table-column>
      <el-table-column label="回答" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.answer }}</span>
        </template>
      </el-table-column>
      <el-table-column align="center" prop="created_at" label="操作">
        <template slot-scope="scope">
          <el-button type="primary" @click="updateBtn(scope.row)">修改</el-button>
          <el-button type="danger" @click="del(scope.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      issue: {
        question: '',
        answer: '',
        _id: ''
      },
      list: [],
      listLoading: true,
      dialogFormVisible: false,
      formLabelWidth: '70px',
      isUpdate: false
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      this.listLoading = false
      this.$http({
        path: 'http://localhost:3001/admin/issue/findAll',
        method: 'get'
      }).then(res => {
        this.list = res.data.result
        console.log(this.list)
      })
    },
    onSubmit() {
      this.$message('submit!')
    },
    onCancel() {
      this.$message({
        message: 'cancel!',
        type: 'warning'
      })
    },
    add() {
      if (!this.issue.question || !this.issue.answer) {
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        this.$http({
          path: '/admin/issue/add',
          method: 'post',
          params: {
            question: this.issue.question,
            answer: this.issue.answer
          }
        }).then(res => {
          console.log(res)
          this.dialogFormVisible = false
          this.$message({
            message: res.data.msg,
            type: res.data.code === 200 ? 'success' : 'error'
          })
          this.fetchData()
        })
      }
    },
    addBtn() {
      this.dialogFormVisible = true
      this.isUpdate = false
      this.issue = {}
    },
    updateBtn(row) {
      this.isUpdate = true
      this.dialogFormVisible = true
      this.$http({
        path: '/admin/issue/findOne',
        method: 'get',
        params: {
          _id: row._id
        }
      }).then(res => {
        const issue = res.data.result
        this.issue._id = issue._id
        this.issue.question = issue.question
        this.issue.answer = issue.answer
      })
    },
    update() {
      if (!this.issue.question || !this.issue.answer) {
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        this.$http({
          path: '/admin/issue/update',
          method: 'post',
          params: {
            _id: this.issue._id,
            question: this.issue.question,
            answer: this.issue.answer
          }
        }).then(res => {
          console.log(res)
          this.dialogFormVisible = false
          this.fetchData()
          this.$message({
            message: res.data.msg,
            type: res.data.code === 200 ? 'success' : 'error'
          })
        })
      }
    },
    del(row) {
      this.$confirm('确定要删除吗？', '提示', {
        confirmButtonText: '删除',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          path: '/admin/issue/del',
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

<style scoped>
.line{
  text-align: center;
}
</style>

