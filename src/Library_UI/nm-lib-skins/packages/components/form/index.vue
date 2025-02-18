<template>
  <el-form
    class="nm-form"
    ref="form"
    :model="model"
    :rules="rules"
    :label-width="labelWidth"
    :size="fontSize"
    :inline="inline"
    :disabled="disabled"
    v-loading="showLoading"
    :element-loading-text="loadingText"
    :element-loading-background="loadingBackground"
    :element-loading-spinner="loadingSpinner"
  >
    <slot/>
  </el-form>
</template>
<script>
import loading from '../../mixins/components/loading'
export default {
  name: 'Form',
  mixins: [loading],
  data() {
    return {
      loading_: false
    }
  },
  props: {
    /** 表单对象 */
    model: {
      type: Object,
      required: true
    },
    /** 验证规则 */
    rules: Object,
    /** 提交请求 */
    action: Function,
    /** 行内表单模式 */
    inline: Boolean,
    /** 是否显示成功提示消息 */
    successMsg: {
      type: Boolean,
      default: true
    },
    /** 成功提示消息文本 */
    successMsgText: {
      type: String,
      default: '保存成功'
    },
    /** 标签的宽度 */
    labelWidth: {
      type: String,
      default: '100px'
    },
    // 自定义验证
    validate: Function,
    /** 禁用表单 */
    disabled: Boolean,
    /** 显示加载动画 */
    loading: Boolean,
    /** 不显示加载动画 */
    noLoading: Boolean
  },
  computed: {
    showLoading() {
      return !this.noLoading && (this.loading_ || this.loading)
    }
  },
  methods: {
    /** 提交 */
    submit() {
      this.$refs.form.validate(async (valid) => {
        if (valid) {
          // 自定义验证
          if (!this.validate || this.validate() === true) {
            this.openLoading()

            this.action(this.model).then(data => {
              if (this.successMsg === true) {
                this._success(this.successMsgText)
              }

              this.$emit('success', data)

              this.closeLoading()
            }).catch(() => {
              this.$emit('error')
              this.closeLoading()
            })
          }
        } else {
          // 验证失败
          this.$emit('validate-error')
        }
      })
    },
    /** 重置 */
    reset() {
      this.$refs.form.resetFields()
    },
    /** 清除验证结果 */
    clearValidate(props) {
      this.$refs.form.clearValidate(props)
    },
    /** 打开加载中 */
    openLoading() {
      if (!this.noLoading) { this.loading_ = true }
    },
    /** 关闭加载中 */
    closeLoading() {
      if (!this.noLoading) { this.loading_ = false }
    }
  }
}
</script>
