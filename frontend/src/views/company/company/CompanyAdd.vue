<template>
  <a-drawer
    title="新增客户"
    :maskClosable="false"
    width=1000
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="companyAddVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;">
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label='名称'
                      v-bind="formItemLayout"
                      :validateStatus="validateStatus"
                      :help="help">
            <a-input v-model="company.companyName"
                    @blur="handleCompanyNameBlur"
                    style="margin-left:5px"
                    v-decorator="['companyName',{rules: [{ required: true, message: '客户名不能为空'}]}]"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="性质" v-bind="formItemLayout">
            <a-input style="margin-left:8px" v-decorator="['nature']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="规模" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['scale']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="12">
          <a-form-item label="融资情况" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['finance']"/>
          </a-form-item>
        </a-col>
        <a-col :md="12">
          <a-form-item label="地址" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyAddress']"/>
          </a-form-item>
        </a-col>
      </a-row>
    
       <a-row>
        <a-col :md="8">
          <a-form-item label="标签1" v-bind="formItemLayout">
            <a-input style="margin-left:8px" v-decorator="['tag1']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="标签2" v-bind="formItemLayout">
            <a-input style="margin-left:8px" v-decorator="['tag2']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="标签3" v-bind="formItemLayout">
            <a-input style="margin-left:8px" v-decorator="['tag3']"/>
          </a-form-item>
        </a-col>
      </a-row>

       <a-row>
        <a-col :md="24">
          <a-form-item style="margin-left:-55px" label="公司介绍" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'info',
              {rules: [
                { max: 100, company: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>
    </a-form>
      <div class="drawer-bootom-button">
        <a-popconfirm title="确定放弃编辑？" @confirm="onClose" okText="确定" cancelText="取消">
          <a-button style="margin-right: .8rem">取消</a-button>
        </a-popconfirm>
        <a-button @click="handleSubmit" type="primary" :loading="loading">提交</a-button>
      </div>
  </a-drawer>
</template>
<script>

const formItemLayout = {
  labelCol: { span: 3 },
  wrapperCol: { span: 18 }
}

export default {
  name: 'CompanyAdd',
  props: {
    companyAddVisiable: {
      default: false
    }
  },
  data () {
    return {
      loading: false,
      formItemLayout,
      form: this.$form.createForm(this),
      company: {
        companyName: ''
      },
      validateStatus: '',
      help: ''
    }
  },
  methods: {
    reset () {
      this.loading = false
      this.form.resetFields()
      this.validateStatus = ''
      this.help = ''
      this.company.companyName = ''
    },
    handleCompanyNameBlur () {
      let companyName = this.company.companyName.trim()
      if (companyName.length) {
        if (companyName.length > 20) {
          this.validateStatus = 'error'
          this.help = '用户名不能超过20个字符'
        } else if (username.length < 4) {
          this.validateStatus = 'error'
          this.help = '用户名不能少于4个字符'
        } else {
          this.validateStatus = 'validating'
          this.$get(`company/check/${companyName}`).then((r) => {
            if (r.data) {
              this.validateStatus = 'success'
              this.help = ''
            } else {
              this.validateStatus = 'error'
              this.help = '抱歉，该公司已存在'
            }
          })
        }
      } else {
        this.validateStatus = 'error'
        this.help = '公司名称不能为空'
      }
    },
    onClose () {
      this.reset()
      this.$emit('close')
    },
    onBirthdayChange(dates, dateStrings) {
      this.birthday = dateStrings
    },
    onGraduateTimeChange(dates, dateStrings) {
      this.graduateTime = dateStrings
    },
    handleSubmit () {
      if (this.validateStatus !== 'success') {
        this.handleCompanyNameBlur()
      }
      this.form.validateFields((err, values) => {
        if (!err && this.validateStatus === 'success') {
          this.loading = true
          this.company = Object.assign(values)
          if (!err) {
            this.$post('company', {
              ...this.company
            }).then(() => {
              this.reset()
              this.$emit('success')
            }).catch(() => {
              this.loading = false
            })
          }
         }
      })
    }
  },
}
</script>
