<template>
  <a-drawer
    title="修改简历信息"
    :maskClosable="false"
    width="1450"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="resumeEditVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label="期望行业:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['trade']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="期望职能" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['functional']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="期望地点" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['workAddress']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="期望年薪:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['annualSalary']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="目前年薪:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['currentSalary']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="标签:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['labelName']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="公司名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="公司行业:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyTrade']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="职位名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['positionName']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="工作地点:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyAddress']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="下属人数:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['underNumber']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="任职时间:" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['trade']"/> -->
            <a-range-picker
              :defaultValue="[moment(this.employmentPeriodFrom, dateFormat), moment(this.employmentPeriodTo, dateFormat)]"
              :format="dateFormat"
              :ranges="{ Today: [moment(), moment()], 'This Month': [moment(), moment().endOf('month')] }"
              @change="onEmploymentChange"
            />
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item style="margin-left:-125px" label="职责业绩:" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['jobPerformance']"/> -->
            <a-textarea
              :rows="4"
              v-decorator="[
              'jobPerformance',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="公司性质:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyNature']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="公司规模:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyScale']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item style="margin-left:-125px" label="公司简介:" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['companyIntro']"/> -->
            <a-textarea
              :rows="4"
              v-decorator="[
              'companyIntro',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="所在部门:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['department']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="月薪:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['monthlyPayNumber']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="月薪数:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['monthlyPay']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="学校名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['schoolName']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="专业名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['majorName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="就读时间:" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['trade']"/> -->
            <a-range-picker
              :defaultValue="[moment(this.studyStart, dateFormat), moment(this.studyEnd, dateFormat)]"
              :format="dateFormat"
              :ranges="{ Today: [moment(), moment()], 'This Month': [moment(), moment().endOf('month')] }"
              @change="onStudyChange"
            />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="学历:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['education']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="是否统招:" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'recruitmentFlag',
                {rules: [{ required: true, message: '请选择统招情况' }]}
              ]"
            >
              <a-radio value="Y">是</a-radio>
              <a-radio value="N">否</a-radio>
            </a-radio-group>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="精通语言:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['language']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="熟练程度:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['proficiency']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="等级:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['grade']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="项目名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['projectName']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="工作时间:" v-bind="formItemLayout">
            <a-range-picker
              :defaultValue="[moment(this.timeStart, dateFormat), moment(this.timeEnd, dateFormat)]"
              :format="dateFormat"
              :ranges="{ Today: [moment(), moment()], 'This Month': [moment(), moment().endOf('month')] }"
              @change="onWorkTimeChange"
            />
          </a-form-item>
        </a-col>
        <a-col :md="24">
          <a-form-item style="margin-left:-125px" label="项目描述:" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['description']"/> -->
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
        <a-col :md="24">
          <a-form-item style="margin-left:-125px" label="项目职责:" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['duty']"/> -->
            <a-textarea
              :rows="4"
              v-decorator="[
              'duty',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
        <a-col :md="24">
          <a-form-item label="项目业绩:" style="margin-left:-125px" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['performance']"/> -->
            <a-textarea
              :rows="4"
              v-decorator="[
              'performance',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
        <!-- <a-col :md="8">
          <a-form-item label="自我评价:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['selfAssessment']"/>
          </a-form-item>
        </a-col>
        <a-row>-->
        <a-col :md="24">
          <a-form-item label="自我评价" style="margin-left:-125px" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'selfAssessment',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
            ></a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="附加信息:" style="margin-left:-125px" v-bind="formItemLayout">
            <!-- <a-input style="margin-left:5px" v-decorator="['addInformation']"/> -->
            <a-col :md="24">
              <a-textarea
                :rows="4"
                v-decorator="[
              'addInformation',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]"
              ></a-textarea>
            </a-col>
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

const formItemLayout = {
  labelCol: { span: 3 },
  wrapperCol: { span: 18 }
};
export default {
  name: "ResumeEdit",
  props: {
    resumeEditVisiable: {
      default: false
    }
  },
  data() {
    return {
      formItemLayout,
      dateFormat: "YYYY/MM/DD",
      form: this.$form.createForm(this),
      resumeId: "",
      loading: false,
      resumeName: "",
      resume: {},
      employmentPeriodFrom: "",
      employmentPeriodTo: "",
      timeStart: "",
      timeEnd: "",
      studyStart: "",
      studyEnd: ""
    };
  },
  // computed: {
  //   ...mapState({
  //     currentresume: state => state.account.resume
  //   })
  // },
  methods: {
    // ...mapMutations({
    //   setresume: "account/setresume"
    // }),
    moment,
    onClose() {
      this.loading = false;
      this.form.resetFields();
      this.$emit("close");
    },
    onStudyChange(dates, dateStrings) {
      // console.log('From: ', dates[0], ', to: ', dates[1]);
      // console.log('From: ', dateStrings[0], ', to: ', dateStrings[1]);
      this.studyStart = dateStrings[0];
      this.studyEnd = dateStrings[1];
    },
    onEmploymentChange(dates, dateStrings) {
      this.employmentPeriodFrom = dateStrings[0];
      this.employmentPeriodTo = dateStrings[1];
    },
    onWorkTimeChange(dates, dateStrings) {
      this.timeStart = dateStrings[0];
      this.timeEnd = dateStrings[1];
    },
    setFormValues({ ...resume }) {
      this.resumeId = resume.resumeId;
      this.employmentPeriodFrom = resume.employmentPeriodFrom;
      this.employmentPeriodTo = resume.employmentPeriodTo;
      this.timeStart = resume.timeStart;
      this.timeEnd = resume.timeEnd;
      this.studyStart = resume.studyStart;
      this.studyEnd = resume.studyEnd;
      let fields = [
        "profession",
        "userAddress",
        "selfAssessment",
        "status",
        "account",
        "schoolName",
        "majorName",
        "studyStart",
        "studyEnd",
        "education",
        "recruitmentFlag",
        "userCompany",
        "companyTrade",
        "positionName",
        "companyAddress",
        "underNumber",
        "employmentPeriodFrom",
        "employmentPeriodTo",
        "jobPerformance",
        "department",
        "companyNature",
        "companyScale",
        "companyIntro",
        "monthlyPayNumber",
        "monthlyPay",
        "addInformation",
        "labelName",
        "language",
        "proficiency",
        "grade",
        "projectName",
        "companyName",
        "timeStart",
        "timeEnd",
        "description",
        "duty",
        "performance",
        "resumeName",
        "trade",
        "functional",
        "workAddress",
        "annualSalary",
        "currentSalary"
      ];
      Object.keys(resume).forEach(key => {
        if (fields.indexOf(key) !== -1) {
          this.form.getFieldDecorator(key);
          let obj = {};
          obj[key] = resume[key];
          this.form.setFieldsValue(obj);
        }
      });
    },
    onBirthdayChange(dates, dateStrings) {
      this.birthday = dateStrings;
    },
    onGraduateTimeChange(dates, dateStrings) {
      this.graduateTime = dateStrings;
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        if (!err) {
          let resume = this.form.getFieldsValue();
          resume.resumeId = this.resumeId;
          resume.resumeId = this.resumeId;
          resume.employmentPeriodFrom = this.employmentPeriodFrom;
          resume.employmentPeriodTo = this.employmentPeriodTo;
          resume.timeStart = this.timeStart;
          resume.timeEnd = this.timeEnd;
          resume.studyStart = this.studyStart;
          resume.studyEnd = this.studyEnd;
          this.$put("resume", {
            ...resume
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
