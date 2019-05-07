<template>
  <a-drawer
    title="修改公司信息"
    :maskClosable="false"
    width="900"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="companyEditVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label="名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" readonly v-decorator="['companyName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="地址" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyAddress']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="融资情况" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['finance']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="规模" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['scale']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="性质" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['nature']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="标签1" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['tag1']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="标签2" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['tag2']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="标签3" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['tag3']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="介绍" v-bind="formItemLayout">
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
import { mapState, mapMutations } from "vuex";
import moment from 'moment';

const formItemLayout = {
  labelCol: { span: 3 },
  wrapperCol: { span: 18 }
};
export default {
  name: "CompanyEdit",
  props: {
    companyEditVisiable: {
      default: false
    }
  },
  data() {
    return {
      formItemLayout,
      dateFormat: 'YYYY/MM/DD',
      form: this.$form.createForm(this),
      companyId: "",
      loading: false,
      birthday:'',
      companyName:'',
      company: {},
      graduateTime: '',
    };
  },
  // computed: {
  //   ...mapState({
  //     currentcompany: state => state.account.company
  //   })
  // },
  methods: {
    // ...mapMutations({
    //   setcompany: "account/setcompany"
    // }),
    moment,
    onClose() {
      this.loading = false;
      this.form.resetFields();
      this.$emit("close");
    },
    setFormValues({ ...company }) {
      this.companyId = company.companyId;
      let fields = ["companyName", "avatar", "companyAddress",
       "finance", "scale","nature","info","tag1","tag2","tag3","createTime"];
      Object.keys(company).forEach(key => {
        if (fields.indexOf(key) !== -1) {
          this.form.getFieldDecorator(key);
          let obj = {};
          obj[key] = company[key];
          this.form.setFieldsValue(obj);
        }
      });
    },
    onBirthdayChange(dates, dateStrings) {
      this.birthday = dateStrings
      console.log(this.birthday)
    },
    onGraduateTimeChange(dates, dateStrings) {
      this.graduateTime = dateStrings
      console.log(this.graduateTime)
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        if (!err) {
          this.loading = true;
          let company = this.form.getFieldsValue();
          company.companyId = this.companyId;
          this.$put("company", {
            ...company
          })
            .then(r => {
              this.loading = false;
              this.$emit("success");
            })
            .catch(() => {
              this.loading = false;
            });
        }
      });
    }
  },
};
</script>
