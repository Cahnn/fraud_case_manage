<template>
  <div class="case-edit">
    <el-form ref="ruleForm" class="form" :model="form" label-width="80px">
      <el-form-item label="标题" required>
        <el-input v-model="form.title" maxlength="20" show-word-limit style="width: 500px"/>
      </el-form-item>
      <el-form-item label="作者" required>
        <el-input v-model="form.author" style="width: 500px" />
      </el-form-item>
      <el-form-item label="来源" required>
        <el-input v-model="form.stemfrom" maxlength="10" show-word-limit style="width: 500px" />
      </el-form-item>
      <el-form-item label="案发时间" required>
        <el-date-picker
          v-model="form.timeofcrime"
          type="date"
          placeholder="选择日期"
        />
      </el-form-item>
      <el-form-item label="封面图片" required>
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

      <el-form-item label="案发省份" required>
        <el-select v-model="form.province" filterable placeholder="请选择">
          <el-option
            v-for="(item,index) in provinces"
            :key="index"
            :value="item"
          />
        </el-select>
      </el-form-item>

      <el-form-item label="受骗金额" required>
        <el-input v-model="form.amount" style="width: 200px" />
      </el-form-item>
      <el-form-item label="年 龄 段" required>
        <el-radio-group v-model="form.age">
          <el-radio label="1">18岁以下</el-radio>
          <el-radio label="2">18-35岁</el-radio>
          <el-radio label="3">36-45岁</el-radio>
          <el-radio label="4">46-60岁</el-radio>
          <el-radio label="5">61岁以上</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="案发类型" required>
        <el-radio-group v-model="form.type">
          <div v-for="(item, index) in category" :key="index" style="display: inline-block">
            <el-radio :label="item.id" style="margin-right: 20px">{{ item.name }}</el-radio>
          </div>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="案发详情" required>
        <div id="editor" />
      </el-form-item>
      <el-form-item>
        <el-button v-if="_id == null" type="primary" @click="onSubmit()">确认添加</el-button>
        <el-button v-else type="primary" @click="onUpdate">确认修改</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
import Editor from 'wangeditor'

export default {
  name: 'CaseEdit',
  data: function() {
    return {
      fileList: [],
      form: {
        title: '',
        cover: '',
        author: this.$store.state.user.name,
        stemfrom: '',
        timeofcrime: '',
        amount: '',
        age: '',
        type: '',
        content: '',
        province: ''
      },
      provinces: ['河北', '山西', '辽宁', '吉林', '黑龙江', '江苏', '浙江', '安徽', '福建',
        '江西', '山东', '河南', '湖北', '湖南', '广东', '海南', '四川', '贵州', '云南', '陕西',
        '甘肃', '青海', '台湾', '内蒙古', '广西', '西藏', '宁夏', '新疆', '北京', '上海', '天津',
        '重庆', '香港', '澳门'],
      // 将editor对象变成vue实例对象
      editor: null,
      // eslint-disable-next-line vue/no-reserved-keys
      _id: '',
      category: []
    }
  },
  watch: {
    form: {
      // 使先获得富文本内容再赋值
      handler(val) {
        // 注册编辑器内容变化事件
        // this.editor.onchange = function() {
        //   // 获取编辑器中的内容
        //   let content = val.content
        //   // 清除不可见的HTML标记和空白字符
        //   content = content.replace(/(<([^>]+)>)/ig, '').replace(/&nbsp;/ig, '')
        //   // 将处理后的内容设置回编辑器中
        //   this.editor.txt.html(content)
        //   console.log('content是什么', content)
        // }
        this.editor.txt.html(val.content)
      },
      deep: true
    }
  },
  created() {
    // 查询单个案例，获取数据回显到表单
    this._id = this.$route.query._id // 编辑按钮传递过来的参数
    if (this._id) {
      this.$http({
        path: '/admin/fraud/findOne',
        method: 'get',
        params: {
          _id: this._id
        }
      }).then(res => {
        console.log(res)
        const fraud = res.data.result
        this.fileList[0] = fraud.cover
        this.form.title = fraud.title
        this.form.cover = fraud.cover
        this.form.stemfrom = fraud.stemfrom
        this.form.timeofcrime = fraud.timeofcrime
        this.form.amount = fraud.amount
        this.form.age = fraud.age
        this.form.type = fraud.type
        this.form.content = fraud.content
        this.form.province = fraud.province
      })
    }

    // 获取类型列表
    this.getCategoryList()
  },
  mounted() {
    // 创建wang editor实例
    this.editor = new Editor('#editor')
    // 配置上传图片的接口地址
    this.editor.config.uploadImgServer = 'http://localhost:3001/upload/editor/img'
    this.editor.config.uploadFileName = 'editorFile'
    // 设置富文本编辑器高度
    // this.editor.config.height = 600
    // this.editor.config.width = 800
    // 设置提示文字
    this.editor.config.placeholder = '编辑文章内容'
    this.editor.create()
  },
  methods: {
    // 转化时间类型
    msToDate(time) {
      const datetime = new Date(time)
      const year = datetime.getFullYear()
      const month = datetime.getMonth()
      const date = datetime.getDate()
      return year +
        '-' +
        ((month + 1) >= 10 ? (month + 1) : '0' + (month + 1)) +
        '-' +
        ((date + 1) < 10 ? '0' + date : date)
    },
    // 获取类型列表
    getCategoryList() {
      this.$http({
        path: '/admin/category/findAll',
        method: 'get'
      }).then(res => {
        console.log(res)
        this.category = res.data.result
      })
    },
    onSubmit() {
      this.form.content = this.editor.txt.html()
      if (!this.form.title || !this.form.stemfrom || !this.form.content || !this.form.cover ||
        !this.form.timeofcrime || !this.form.amount || !this.form.age || !this.form.type || !this.form.province){
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        // 获得富文本编辑器内容
        const date = new Date()
        this.form.timeofcrime = this.msToDate(this.form.timeofcrime)
        const year = (this.form.timeofcrime).slice(0, 4)

        this.$http({
          path: '/admin/fraud/add',
          method: 'post',
          params: {
            title: this.form.title,
            createTime: `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`,
            stemfrom: this.form.stemfrom,
            content: this.form.content,
            author: this.form.author,
            cover: this.form.cover,
            timeofcrime: this.form.timeofcrime,
            amount: this.form.amount,
            age: this.form.age,
            type: this.form.type,
            province: this.form.province,
            year: year
          }
        }).then(res => {
          console.log(res)
          this.$message({
            message: res.data.msg,
            type: res.data.code === 200 ? 'success' : 'error'
          })
        })
        this.$router.push({
          path: '/example/table'
        })
      }
    },
    onSuccess(res) {
      this.fileList[0] = res.data
      console.log(res)
      this.form.cover = this.fileList[0]
    },
    handleRemove(file, fileList) {
      console.log(file, fileList)
    },
    handlePreview(file) {
      console.log(file)
    },
    onUpdate() {
      if (!this.form.title || !this.form.stemfrom || !this.form.content || !this.form.cover ||
        !this.form.timeofcrime || !this.form.amount || !this.form.age || !this.form.type || !this.form.province){
        this.$message({
          message: '必填字段不能为空',
          type: 'error'
        })
      } else {
        // 获得富文本编辑器内容
        const content = this.editor.txt.html()
        this.$http({
          path: '/admin/fraud/update',
          method: 'post',
          params: {
            _id: this._id,
            title: this.form.title,
            stemfrom: this.form.stemfrom,
            content,
            author: this.form.author,
            timeofcrime: this.form.timeofcrime,
            amount: this.form.amount,
            age: this.form.age,
            type: this.form.type,
            province: this.form.province
          }
        }).then(res => {
          console.log(res)
          this.$message({
            message: res.data.msg,
            type: res.data.code === 200 ? 'success' : 'error'
          })
        })
        this.$router.push({
          path: '/example/table'
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

