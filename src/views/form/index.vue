<template>
  <div class="app-container">
    <el-button type="primary" style="margin-bottom: 30px" @click="addBtn">添加类型</el-button>
    <el-dialog title="添加类型" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="类型名称" :label-width="formLabelWidth" required>
          <el-input v-model="form.name" type="text" name="name" />
        </el-form-item>
        <el-form-item label="类型编号" :label-width="formLabelWidth"  required>
          <el-input v-model="form.id" type="text" name="id" />
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
      <el-table-column label="名称" align="center">
        <template slot-scope="scope">
          {{ scope.row.name }}
        </template>
      </el-table-column>
      <el-table-column label="编号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.id }}</span>
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
      form: {
        name: '',
        id: 0, // 初始值不能是Number
        _id: ''
      },
      list: [],
      listLoading: true,
      dialogFormVisible: false,
      formLabelWidth: '80px',
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
        path: 'http://localhost:3001/admin/category/findAll',
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
      if (!this.form.name || !this.form.id){
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        this.$http({
          path: '/admin/category/add',
          method: 'post',
          params: {
            name: this.form.name,
            id: this.form.id
          }
        }).then(res => {
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
      this.form = {}
    },
    updateBtn(row) {
      this.isUpdate = true
      this.dialogFormVisible = true
      this.$http({
        path: '/admin/category/findOne',
        method: 'get',
        params: {
          _id: row._id
        }
      }).then(res => {
        const category = res.data.result
        this.form._id = category._id
        this.form.name = category.name
        this.form.id = category.id
      })
    },
    update() {
      if (!this.form.name || !this.form.id){
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        this.$http({
          path: '/admin/category/update',
          method: 'post',
          params: {
            _id: this.form._id,
            name: this.form.name,
            id: this.form.id
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
          path: '/admin/category/del',
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

