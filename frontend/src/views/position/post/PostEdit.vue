<template>
  <a-drawer
    title="修改项目信息"
    :maskClosable="false"
    width="600"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="postEditVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col>
          <a-form-item label="职位标题:" v-bind="formItemLayout">
            <a-input v-decorator="['positionName']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col>
          <a-form-item label="招聘地址" v-bind="formItemLayout">
            <a-input v-decorator="['address']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col>
          <a-form-item label="学历要求" v-bind="formItemLayout">
            <education-background-input-tree
              @change="onEducationBackgroundChange"
              :message="needEducation"
              ref="educationBackgroundTree"
            ></education-background-input-tree>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col>
          <a-form-item label="薪资范围" v-bind="formItemLayout">
            <salary-input-tree @change="onSalaryChange" :message="salary" ref="salaryTree"></salary-input-tree>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col>
          <a-form-item label="公司名称" v-bind="formItemLayout">
            <a-input v-decorator="['issueCompany']"/>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row>
        <a-col>
          <a-form-item label="工作经验" v-bind="formItemLayout">
            <work-years-input-tree
              @change="onWorkYearsChange"
              :message="needWorkTime"
              ref="workYearsTree"
            ></work-years-input-tree>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col>
          <a-form-item label="职位类型" v-bind="formItemLayout">
            <position-sort-input-tree
              @change="onPositionSortChange"
              :message="positionSort"
              ref="positionSortTree"
            ></position-sort-input-tree>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col>
          <a-form-item label="职位描述" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'description',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col>
          <a-form-item label="职责要求" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'requested',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
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
import moment from "moment";
import EducationBackgroundInputTree from "@/components/dropbox/EducationBackgroundInputTree";
import WorkYearsInputTree from "@/components/dropbox/WorkYearsInputTree";
import PositionSortInputTree from "@/components/dropbox/PositionSortInputTree";
import SalaryInputTree from "@/components/dropbox/SalaryInputTree";

const formItemLayout = {
  labelCol: { span: 3 },
  wrapperCol: { span: 18 }
};
export default {
  name: "PostEdit",
  components: {
    EducationBackgroundInputTree,
    WorkYearsInputTree,
    PositionSortInputTree,
    SalaryInputTree
  },
  props: {
    postEditVisiable: {
      default: false
    }
  },
  data() {
    return {
      formItemLayout,
      dateFormat: "YYYY/MM/DD",
      form: this.$form.createForm(this),
      id: "",
      loading: false,
      position: {},
      positionSort: "",
      salary: "",
      needEducation: "",
      needWorkTime: ""
    };
  },
  // computed: {
  //   ...mapState({
  //     currentposition: state => state.account.position
  //   })
  // },
  methods: {
    // ...mapMutations({
    //   setposition: "account/setposition"
    // }),
    moment,
    onClose() {
      this.loading = false;
      this.form.resetFields();
      this.$emit("close");
    },
    onPositionSortChange(value) {
      this.positionSort = value || "";
    },
    onEducationBackgroundChange(value) {
      this.needEducation = value || "";
    },
    onWorkYearsChange(value) {
      this.needWorkTime = value || "";
    },
    onSalaryChange(value) {
      this.salary = value || "";
    },
    setFormValues({ ...position }) {
      this.id = position.id;
      this.needWorkTime = position.needWorkTime;
      this.salary = position.salary;
      this.needEducation = position.needEducation;
      this.positionSort = position.positionSort;
      let fields = [
        "positionName",
        "address",
        "needEducation",
        "positionSort",
        "salary",
        "issueCompany",
        "issueTime",
        "needWorkTime",
        "description",
        "requested"
      ];
      Object.keys(position).forEach(key => {
        if (fields.indexOf(key) !== -1) {
          this.form.getFieldDecorator(key);
          let obj = {};
          obj[key] = position[key];
          this.form.setFieldsValue(obj);
        }
      });
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        if (!err) {
          this.loading = true;
          let position = this.form.getFieldsValue();
          position.id = this.id;
          position.needWorkTime = this.needWorkTime;
          position.salary = this.salary;
          position.needEducation = this.needEducation;
          position.positionSort = this.positionSort;
          console.log(position);
          this.$put("position", {
            ...position
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
  }
};
</script>
