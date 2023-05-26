<template>
  <div class="app-container">
    <el-button type="primary" style="margin-bottom: 30px" @click="addBtn">添加题目</el-button>
    <el-dialog title="添加题目" :visible.sync="dialogFormVisible">
      <div class="option">
        <el-radio-group v-model="radio">
          <el-radio-button v-for="(item,index) in types" :key="index" :label="item" />
        </el-radio-group>
      </div>

      <el-form v-if="radio === '判断题'" :model="firstQues">
        <el-form-item label="问题" :label-width="formLabelWidth" required>
          <el-input v-model="firstQues.title" type="text" name="question" />
        </el-form-item>
        <el-form-item label="答案" :label-width="formLabelWidth" required>
          <el-input v-model="firstQues.answer" type="text" name="answer" />
        </el-form-item>
        <el-form-item label="解释" :label-width="formLabelWidth" required>
          <el-input v-model="firstQues.explain" type="text" name="explain" />
        </el-form-item>
        <div style="margin-left: 70px;font-size: 12px">提示:默认A选项为对，B选项为错</div>
      </el-form>

      <el-form v-if="radio === '单选题'" :model="secondQues">
        <el-form-item label="问题" :label-width="formLabelWidth" required>
          <el-input v-model="secondQues.title" type="text" name="question" />
        </el-form-item>
        <el-form-item label="选项" :label-width="formLabelWidth" required>
          <el-input v-for="(item,index) in secondQues.optionList" :key="index" v-model="item.content" :placeholder="item.id" type="text" name="option" style="margin-top: 5px" />
        </el-form-item>
        <el-form-item label="答案" :label-width="formLabelWidth" required>
          <el-input v-model="secondQues.answer" type="text" name="answer" />
        </el-form-item>
        <el-form-item label="解释" :label-width="formLabelWidth" required>
          <el-input v-model="secondQues.explain" type="text" name="explain" />
        </el-form-item>
      </el-form>

      <el-form v-if="radio === '多选题'" :model="thirdQues">
        <el-form-item label="问题" :label-width="formLabelWidth" required>
          <el-input v-model="thirdQues.title" type="text" name="question" />
        </el-form-item>
        <el-form-item label="选项" :label-width="formLabelWidth" required>
          <el-input v-for="(item,index) in thirdQues.optionList" :key="index" v-model="item.content" :placeholder="item.id" type="text" name="option" style="margin-top: 5px" />
        </el-form-item>
        <el-form-item label="答案" :label-width="formLabelWidth" required>
          <el-input v-model="thirdQues.answer" type="text" name="answer" />
        </el-form-item>
        <el-form-item label="解释" :label-width="formLabelWidth" required>
          <el-input v-model="thirdQues.explain" type="text" name="explain" />
        </el-form-item>
      </el-form>

      <div slot="footer" class="dialog-footer">
        <el-button @click="cancelDialog">取 消</el-button>
        <el-button v-if="!isUpdate" type="primary" @click="add">确定添加</el-button>
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
          {{ scope.row.title }}
        </template>
      </el-table-column>
      <el-table-column label="选项" align="center">
        <template slot-scope="scope">
          <div v-for="(item,index) in scope.row.optionList" :key="index">{{ item.id }}{{ item.content }}</div>
        </template>
      </el-table-column>
      <el-table-column label="答案" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.answer }}</span>
        </template>
      </el-table-column>
      <el-table-column label="类型" align="center">
        <template slot-scope="scope">
          <span v-if="scope.row.type === 1">判断题</span>
          <span v-if="scope.row.type === 2">单选题</span>
          <span v-if="scope.row.type === 3">多选题</span>
        </template>
      </el-table-column>
      <el-table-column label="解释" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.explain }}</span>
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
      firstQues: {
        type: 1,
        title: '',
        answer: '',
        optionList: [
          {
            id: 'A',
            content: '对'
          },
          {
            id: 'B',
            content: '错'
          }
        ],
        explain: '',
        _id: ''
      },
      secondQues: {
        type: 2,
        title: '',
        answer: '',
        optionList: [
          {
            id: 'A',
            content: ''
          },
          {
            id: 'B',
            content: ''
          },
          {
            id: 'C',
            content: ''
          },
          {
            id: 'D',
            content: ''
          }
        ],
        explain: '',
        _id: ''
      },
      thirdQues: {
        type: 3,
        title: '',
        answer: '',
        optionList: [
          {
            id: 'A',
            content: ''
          },
          {
            id: 'B',
            content: ''
          },
          {
            id: 'C',
            content: ''
          },
          {
            id: 'D',
            content: ''
          }
        ],
        explain: '',
        _id: ''
      },
      list: [],
      listLoading: true,
      dialogFormVisible: false,
      formLabelWidth: '70px',
      isUpdate: false,
      types: ['判断题', '单选题', '多选题'],
      radio: '判断题',
      isEmpty1: '0',
      isEmpty2: '0'
    }
  },
  created() {
    this.fetchData()
  },
  methods: {
    fetchData() {
      this.listLoading = false
      this.$http({
        path: 'http://localhost:3001/admin/qbank/findAll',
        method: 'get'
      }).then(res => {
        this.list = res.data.result
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
    cancelDialog() {
      this.dialogFormVisible = false
      this.firstQues = {}
      this.secondQues = {}
      this.thirdQues = {}
      this.radio = '判断题'
    },
    add() {
      let params = {}
      if (this.radio === '判断题') {
        params = this.firstQues
      } else if (this.radio === '单选题') {
        params = this.secondQues
      } else {
        params = this.thirdQues
      }
      // 先判断标题和答案是否为空
      if (params.title !== '' || params.answer !== '') {
        this.isEmpty1 = '0' // 选项为空则把isEmpty1标记为1
        this.isEmpty2 = '0' // 选项不为空则把isEmpty2标记为0
        for (let i = 0; i < params.optionList.length; i++) { // 循环遍历判断选项内容是否为空
          if (!params.optionList[i].content) {
            this.isEmpty1 = '1'
          } else {
            this.isEmpty2 = '0'
          }
        }
        // 只有当isEmpty1和isEmpty2都为0时，选项内容都不为空
        if (this.isEmpty1 === '0' && this.isEmpty2 === '0') {
          this.$http({
            path: '/admin/qbank/add',
            method: 'post',
            params: {
              title: params.title,
              answer: params.answer,
              type: params.type,
              optionList: params.optionList,
              explain: params.explain
            }
          }).then(res => {
            console.log(res)
            this.dialogFormVisible = false
            this.$message({
              message: res.data.msg,
              type: res.data.code === 200 ? 'success' : 'error'
            })
            this.fetchData()
            this.firstQues = {}
            this.secondQues = {}
            this.thirdQues = {}
            this.radio = '判断题'
          })
        } else {
          this.$message({
            message: '必填项不能为空',
            type: 'warning'
          })
        }
      } else {
        this.$message({
          message: '必填项不能为空',
          type: 'warning'
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
        path: '/admin/qbank/findOne',
        method: 'get',
        params: {
          _id: row._id
        }
      }).then(res => {
        console.log(res)
        if (res.data.result.type === 1) {
          this.radio = '判断题'
          this.firstQues = res.data.result
        } else if (res.data.result.type === 2) {
          this.radio = '单选题'
          this.secondQues = res.data.result
        } else if (res.data.result.type === 3) {
          this.radio = '多选题'
          this.thirdQues = res.data.result
        }
      })
    },
    update() {
      let params = {}
      if (this.radio === '判断题') {
        params = this.firstQues
      } else if (this.radio === '单选题') {
        params = this.secondQues
      } else {
        params = this.thirdQues
      }
      // 先判断标题和答案是否为空
      if (params.title !== '' || params.answer !== '') {
        this.isEmpty1 = '0' // 选项为空则把isEmpty1标记为1
        this.isEmpty2 = '0' // 选项不为空则把isEmpty2标记为0
        for (let i = 0; i < params.optionList.length; i++) { // 循环遍历判断选项内容是否为空
          if (!params.optionList[i].content) {
            this.isEmpty1 = '1'
          } else {
            this.isEmpty2 = '0'
          }
        }
        // 只有当isEmpty1和isEmpty2都为0时，选项内容都不为空
        if (this.isEmpty1 === '0' && this.isEmpty2 === '0') {
          this.$http({
            path: '/admin/qbank/update',
            method: 'post',
            params: {
              _id: params._id,
              title: params.title,
              answer: params.answer,
              optionList: params.optionList,
              explain: params.explain
            }
          }).then(res => {
            this.dialogFormVisible = false
            this.fetchData()
            this.$message({
              message: res.data.msg,
              type: res.data.code === 200 ? 'success' : 'error'
            })
            // 更新完后清空输入框
            this.firstQues = {}
            this.secondQues = {}
            this.thirdQues = {}
            this.radio = '判断题'
          })
        } else {
          this.$message({
            message: '必填项不能为空',
            type: 'warning'
          })
        }
      } else {
        this.$message({
          message: '必填项不能为空',
          type: 'warning'
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
          path: '/admin/qbank/del',
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
.option{
  margin-left: 70px;
  margin-bottom: 20px;
}
.line{
  text-align: center;
}
</style>

