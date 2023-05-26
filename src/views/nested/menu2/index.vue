<template>
  <div class="activity-edit">
    <el-form ref="form" class="form" :model="form" label-width="80px">
      <el-form-item label="活动标题" required>
        <el-input id="title" v-model="form.title" style="width: 500px" />
      </el-form-item>
      <el-form-item label="活动封面" required>
        <el-upload
          v-if="_id == null"
          class="upload-demo"
          action="http://localhost:3001/upload/img"
          name="myfile"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :on-success="onSuccess"
          :file-list="fileList"
          list-type="picture"
          :limit="1"
        >
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传一张jpg/png文件，且不超过500kb</div>
        </el-upload>
        <el-upload
          v-else
          class="upload-demo"
          action="http://localhost:3001/upload/img"
          name="myfile"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :on-success="onSuccess"
          :file-list="fileList"
          list-type="picture"
          :limit="1"
          disabled
        >
          <el-button size="small" type="info">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">不能修改封面图片</div>
        </el-upload>
      </el-form-item>
      <el-form-item label="活动内容" required>
        <div id="editor" />
      </el-form-item>
      <el-form-item>
        <el-button v-if="_id == null" type="primary" @click="onSubmit">确认添加</el-button>
        <el-button v-else type="primary" @click="onUpdate">确认修改</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import Editor from 'wangeditor'
export default {
  name: 'ActivityEdit',
  data: function() {
    return {
      fileList: [],
      form: {
        title: '',
        cover: '',
        content: ''
      },
      // 将editor对象变成vue实例对象
      editor: null,
      // eslint-disable-next-line vue/no-reserved-keys
      _id: ''
    }
  },
  watch: {
    form: {
      // 使先获得富文本内容再赋值
      handler(val) {
        // 初始化富文本编辑器内容
        this.editor.onchange = function() {
          // eslint-disable-next-line no-unused-vars
          let cont = this.editor.txt.html(this.form.content)
          cont = cont.replace(/(<([^>]+)>)/ig, '').replace(/&nbsp;/ig, '')
          this.editor.txt.html(cont)
        }
      },
      deep: true
    }
  },
  created() {
    this._id = this.$route.query._id // 编辑按钮传递过来的参数
    if (this._id) {
      this.$http({
        path: '/admin/activity/findOne',
        method: 'get',
        params: {
          _id: this._id
        }
      }).then(res => {
        console.log('待修改的内容', res)
        const activity = res.data.result
        this.form.title = activity.title
        this.form.content = activity.content
      })
    }
  },
  mounted() {
    // 创建wangeditor实例
    this.editor = new Editor('#editor')
    // 配置上传图片的接口地址
    this.editor.config.uploadImgServer = 'http://localhost:3001/upload/editor/img'
    this.editor.config.uploadFileName = 'editorFile'
    // 设置富文本编辑器高度
    // this.editor.config.height = 600
    // this.editor.config.width = 800
    // 设置提示文字
    this.editor.config.placeholder = '编辑活动内容'
    this.editor.create()
  },
  methods: {
    // 添加接口
    onSubmit() {
      if (!this.form.title || !this.form.content || !this.form.cover) {
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        // 获得富文本编辑器内容
        const date = new Date()
        this.form.content = this.editor.txt.html()
        this.$http({
          path: '/admin/activity/add',
          method: 'post',
          params: {
            title: this.form.title,
            createTime: `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`,
            content: this.form.content,
            cover: this.form.cover
          }
        }).then(res => {
          console.log(res)
          this.$message({
            message: res.data.msg,
            type: res.data.code === 200 ? 'success' : 'error'
          })
        })
        this.$router.push({
          path: '/nested/menu1'
        })
      }
    },
    onSuccess(res) {
      this.fileList[0] = res.data
      this.form.cover = this.fileList[0]
    },
    handleRemove(file, fileList) {
      console.log(file, fileList)
    },
    handlePreview(file) {
      console.log(file)
    },
    // 修改接口
    onUpdate() {
      if (!this.form.title || !this.form.content || !this.form.cover) {
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        // 获得富文本编辑器内容
        const content = this.editor.txt.html()
        this.$http({
          path: '/admin/activity/update',
          method: 'post',
          params: {
            _id: this._id,
            title: this.form.title,
            content
          }
        }).then(res => {
          this.$message({
            message: res.data.msg,
            type: res.data.code === 200 ? 'success' : 'error'
          })
        })
        this.$router.push({
          path: '/nested/menu1'
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
form{
  margin: 60px 0 0 100px;
}
.small-img{
  width: 100px;
  height: 100px;
}
::v-deep .is-success{
  width: 400px;
}
::v-deep .el-upload-list__item-name {
  width: 300px;
}
#editor{
  width: 70%;
}
</style>

