<template>
  <div id="editor">
    <div ref="editorElem" style="text-align:left" />
  </div>
</template>

<script>
import Editor from 'wangeditor'
export default {
  name: 'EditorElem',
  // eslint-disable-next-line vue/require-prop-types
  props: ['catchData', 'content'],
  data() {
    return {
      editor: null,
      editorContent: ''
    }
  }, // 接收父组件的方法
  watch: {
    content() {
      if (!this.content) { // 这是为了解决输入时光标乱跳
        this.editor.txt.html(this.content)
      }
    }
  },
  mounted() {
    this.editor = new Editor(this.$refs.editorElem)
    // 配置上传图片的接口地址
    this.editor.config.uploadImgServer = 'http://localhost:3001/upload/editor/img'
    this.editor.config.uploadFileName = 'editorFile'

    this.editor.customConfig.onchange = (html) => {
      this.editorContent = html
      this.catchData(this.editorContent) // 把这个html通过catchData的方法传入父组件
    }
    this.editor.customConfig.menus = [ // 菜单配置
      'head', // 标题
      'bold', // 粗体
      'fontSize', // 字号
      'fontName', // 字体
      'italic', // 斜体
      'underline', // 下划线
      'strikeThrough', // 删除线
      'foreColor', // 文字颜色
      'backColor', // 背景颜色
      'link', // 插入链接
      'list', // 列表
      'justify', // 对齐方式
      'quote', // 引用
      'emoticon', // 表情
      'image', // 插入图片
      'table', // 表格
      'code', // 插入代码
      'undo', // 撤销
      'redo' // 重复
    ]
    this.editor.create() // 创建富文本实例
    this.editor.txt.html(this.content)
  }
}
</script>

<style lang="scss" rel="stylesheet/scss">
.w-e-text-container{
  height: 700px !important;/*!important是重点，因为原div是行内样式设置的高度300px*/
}
</style>
