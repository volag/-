<template>
  <a-drawer
    title="修改项目信息"
    :maskClosable="false"
    width="1450"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="projectEditVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label="名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" readonly v-decorator="['projectName']"/>
          </a-form-item>
        </a-col>
        <!-- <a-col :md="8">
          <a-form-item label="性别" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'projectSex',
                {rules: [{ required: true, message: '请选择性别' }]}
              ]"
            >
              <a-radio value="0">男</a-radio>
              <a-radio value="1">女</a-radio>
              <a-radio value="2">保密</a-radio>
            </a-radio-group>
          </a-form-item>
        </a-col> -->
        <a-col :md="8">
          <a-form-item label="编号" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="projectSort"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['projectCode']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="状态" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="projectSort"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['projectStatus']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="优先级别" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="projectLevel"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['priorityLevel']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="开始日期" v-bind="formItemLayout">
              <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.startTime, dateFormat)"
                    :getPopupContainer="trigger => trigger.parentNode"
                    @change="onStartTimeChange"
                  />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="计划完成日期" v-bind="formItemLayout">
              <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.planFinishTime, dateFormat)"
                    :getPopupContainer="trigger => trigger.parentNode"
                    @change="onPlanFinishTimeChange"
                  />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户名称" v-bind="formItemLayout">
            <!-- <a-tree-select style="margin-left:5px"
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="workYears"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['companyName']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="收款状态" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['gatherStatus']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户经理" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['customerManager']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="业务人员" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['businessPerson']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="项目主管" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="educationBackground"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['projectCharge']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="项目顾问" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['projectCounselor']"/>
          </a-form-item>
        </a-col>
       <a-col :md="8">
          <a-form-item label="客户联系人" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyContact']"/>
          </a-form-item>
        </a-col>
      </a-row>


      <a-row>
        <a-col :md="8">
          <a-form-item label="寻访员" v-bind="formItemLayout">
             <a-input style="margin-left:5px" v-decorator="['searchPerson']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户付款人" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['companyPayer']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="汇报对象" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['reportObject']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
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
        <a-col :md="8">
          <a-form-item label="招聘人数" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['number']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="行业" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['trade']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="适合职能" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['suitableFunction']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="外语要求" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['languageRequire']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="性别要求" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['sexRequire']"/>
          </a-form-item>
        </a-col>
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
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="年龄要求" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['ageRequire']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="薪资范围" v-bind="formItemLayout">
             <a-input style="margin-left:5px" v-decorator="['salaryRange']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="奖金" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['bonus']"/>
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
              ]}]">
            </a-textarea>
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
              ]}]">
            </a-textarea>
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
              ]}]">
            </a-textarea>
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
              ]}]">
            </a-textarea>
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
  name: "ProjectEdit",
  props: {
    projectEditVisiable: {
      default: false
    }
  },
  data() {
    return {
      formItemLayout,
      dateFormat: 'YYYY/MM/DD',
      form: this.$form.createForm(this),
      projectId: "",
      loading: false,
      startTime:'',
      planFinishTime: '',
      projectName:'',
      project: {},
    };
  },
  // computed: {
  //   ...mapState({
  //     currentproject: state => state.account.project
  //   })
  // },
  methods: {
    // ...mapMutations({
    //   setproject: "account/setproject"
    // }),
    moment,
    onClose() {
      this.loading = false;
      this.form.resetFields();
      this.$emit("close");
    },
    setFormValues({ ...project }) {
      this.projectId = project.projectId;
      this.startTime = project.startTime;
      this.planFinishTime = project.planFinishTime;
      let fields = ["projectName", "projectCode", "startTime",
       "planFinishTime", "projectStatus","priorityLevel","companyName",
       "gatherStatus","customerManager","businessPerson",
       "projectCharge","projectCounselor","companyContact","searchPerson","companyPayer",
       "companyInfo","reportObject","subordinatePosition","workAddress","number","trade",
       "suitableFunction","languageRequire","sexRequire","positionLevel","positionSort",
       "ageRequire","positionInfo","positionReason","salaryRange","bonus",
       "welfare","progress","executionAnalyze"];
      Object.keys(project).forEach(key => {
        if (fields.indexOf(key) !== -1) {
          this.form.getFieldDecorator(key);
          let obj = {};
          obj[key] = project[key];
          this.form.setFieldsValue(obj);
        }
      });
    },
    onStartTimeChange(dates, dateStrings) {
      this.startTime = dateStrings;
    },
    onPlanFinishTimeChange(dates, dateStrings) {
      this.planFinishTime = dateStrings;
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        if (!err) {
          this.loading = true;
          let project = this.form.getFieldsValue();
          project.projectId = this.projectId;
          this.$put("project", {
            ...project
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
