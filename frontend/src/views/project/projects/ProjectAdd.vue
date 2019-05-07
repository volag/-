<template>
  <a-drawer
    title="新增人才"
    :maskClosable="false"
    width="1450"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="projectAddVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label="项目名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['projectName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="项目状态" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'projectStatus',
                {rules: [{ required: true, message: '请选择状态' }]}
              ]"
            >
              <a-radio value="0">进行中</a-radio>
              <a-radio value="1">完成</a-radio>
              <a-radio value="2">保密</a-radio>
            </a-radio-group>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="收款状态" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'gatherStatus',
                {rules: [{ required: true, message: '请选择状态' }]}
              ]"
            >
              <a-radio value="0">未收款</a-radio>
              <a-radio value="1">已收款</a-radio>
            </a-radio-group>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="项目编号" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="projectSort"
            ></a-tree-select>-->
            <a-input style="margin-left:5px" v-decorator="['projectCode']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户名称" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="projectLevel"
            ></a-tree-select>-->
            <a-input style="margin-left:5px" v-decorator="['companyName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="优先级别" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="projectLevel"
            ></a-tree-select>-->
            <a-input style="margin-left:5px" v-decorator="['priorityLevel']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="开始日期" v-bind="formItemLayout">
            <a-date-picker
              style="margin-left:5px"
              :format="dateFormat"
              :getPopupContainer="trigger => trigger.parentNode"
              @change="onStartTimeChange"
            />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="计划完成日期" v-bind="formItemLayout">
            <a-date-picker
              style="margin-left:5px"
              :format="dateFormat"
              :getPopupContainer="trigger => trigger.parentNode"
              @change="onPlanFinishTimeChange"
            />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户经理" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['customerManager']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="业务人员" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['businessPerson']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="项目主管" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['projectCharge']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="项目顾问" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="educationBackground"
            ></a-tree-select>-->
            <a-input style="margin-left:5px" v-decorator="['projectCounselor']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="客户联系人:" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" 
              v-decorator="[
            'email',
            {rules: [
              { type: 'email', message: '请输入正确的邮箱' },
              { max: 50, message: '长度不能超过50个字符'}
            ]}
          ]"
            />-->
            <a-input style="margin-left:5px" v-decorator="['companyContact']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="寻访员" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px"
              v-decorator="['mobilePhone', {rules: [
                { pattern: '^0?(13[0-9]|15[012356789]|17[013678]|18[0-9]|14[57])[0-9]{8}$', message: '请输入正确的手机号'}
            ]}]"/>-->
            <a-input style="margin-left:5px" v-decorator="['searchPerson']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户付款人" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyPayer']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="汇报对象" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['reportObject']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="下属职位" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['subordinatePosition']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="工作地点" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['workAddress']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="招聘人数" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['number']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="行业" v-bind="formItemLayout">
            <!-- <a-radio-group
              v-decorator="[
                'maritalStatus',
                {rules: [{ required: true, message: '请选择状态' }]}
              ]"
            >
              <a-radio value="0">锁定</a-radio>
              <a-radio value="1">有效</a-radio>
            </a-radio-group>-->
            <a-input style="margin-left:5px" v-decorator="['trade']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="适合职能" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['suitableFunction']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="职位级别" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['positionLevel']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="职位类型" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['positionSort']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="年龄要求" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['ageRequire']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="性别要求" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'sexRequire',
                {rules: [{ required: true, message: '请选择性别' }]}
              ]"
            >
              <a-radio value="0">男</a-radio>
              <a-radio value="1">女</a-radio>
              <a-radio value="2">不限</a-radio>
            </a-radio-group>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="薪资范围" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['salaryRange']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="奖金" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['bonus']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="福利" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['welfare']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="外语要求" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['languageRequire']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="委托前进展" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'progress',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="公司简介" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'companyInfo',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="职位描述" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'positionInfo',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="职位开发原因" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'positionReason',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="执行分析" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'executionAnalyze',
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
import moment from "moment";
import { mapState } from "vuex";

const formItemLayout = {
  labelCol: { span: 3 },
  wrapperCol: { span: 18 }
};

export default {
  name: "ProjectAdd",
  props: {
    projectAddVisiable: {
      default: false
    }
  },
  data() {
    return {
      dateFormat: "YYYY/MM/DD",
      loading: false,
      formItemLayout,
      form: this.$form.createForm(this),
      project: {},
      planFinishTime: "",
      startTime: ""
    };
  },
  computed: {
    ...mapState({
      user: state => state.account.user
    })
  },
  methods: {
    moment,
    reset() {
      this.loading = false;
      this.form.resetFields();
    },
    onClose() {
      this.reset();
      this.$emit("close");
    },
    onStartTimeChange(dates, dateStrings) {
      this.startTime = dateStrings;
    },
    onPlanFinishTimeChange(dates, dateStrings) {
      this.planFinishTime = dateStrings;
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        this.project = Object.assign(values);
        this.project.startTime = this.startTime;
        this.project.userId = this.user.userId;
        this.project.planFinishTime = this.planFinishTime;
        console.log(this.project);
        if (!err) {
          this.$post("project", {
            ...this.project
          })
            .then(() => {
              this.reset();
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
