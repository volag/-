<template>
  <a-drawer
    title="修改人才信息"
    :maskClosable="false"
    width="1450"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="personEditVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label="姓名:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" readonly v-decorator="['personName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="性别" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'personSex',
                {rules: [{ required: true, message: '请选择性别' }]}
              ]"
            >
              <a-radio value="0">男</a-radio>
              <a-radio value="1">女</a-radio>
              <a-radio value="2">保密</a-radio>
            </a-radio-group>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="类别" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="personSort"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['personSort']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="级别" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="personLevel"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['personLevel']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="出生日期" v-bind="formItemLayout">
              <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.birthday, dateFormat)"
                    :getPopupContainer="trigger => trigger.parentNode"
                    @change="onBirthdayChange"
                  />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="工作年限" v-bind="formItemLayout">
            <!-- <a-tree-select style="margin-left:5px"
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="workYears"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['workYears']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="毕业时间" v-bind="formItemLayout">
              <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.graduateTime, dateFormat)"
                    :getPopupContainer="trigger => trigger.parentNode"
                    @change="onGraduateTimeChange"
                  />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="毕业院校" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['graduateInstitutions']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="专业" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['major']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="学历" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="educationBackground"
            ></a-tree-select> -->
            <a-input style="margin-left:5px" v-decorator="['educationBackground']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="邮箱:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" 
              v-decorator="[
            'email',
            {rules: [
              { type: 'email', message: '请输入正确的邮箱' },
              { max: 50, message: '长度不能超过50个字符'}
            ]}
          ]"
            />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="手机" v-bind="formItemLayout">
            <a-input style="margin-left:5px"
              v-decorator="['mobilePhone', {rules: [
                { pattern: '^0?(13[0-9]|15[012356789]|17[013678]|18[0-9]|14[57])[0-9]{8}$', message: '请输入正确的手机号'}
              ]}]"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="办公电话" v-bind="formItemLayout">
            <a-input style="margin-left:5px"
              v-decorator="['workPhone', {rules: [
                { pattern: '^0?(13[0-9]|15[012356789]|17[013678]|18[0-9]|14[57])[0-9]{8}$', message: '请输入正确的手机号'}
              ]}]"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="家庭电话" v-bind="formItemLayout">
            <a-input style="margin-left:5px"
              v-decorator="['familyPhone', {rules: [
                { pattern: '^0?(13[0-9]|15[012356789]|17[013678]|18[0-9]|14[57])[0-9]{8}$', message: '请输入正确的手机号'}
              ]}]"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="身份证号" v-bind="formItemLayout">
            <a-input style="margin-left:5px"
              v-decorator="['identityCard', {rules: [
                { pattern: '^0?(13[0-9]|15[012356789]|17[013678]|18[0-9]|14[57])[0-9]{8}$', message: '请输入正确的手机号'}
              ]}]"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="国籍" v-bind="formItemLayout">
             <a-input style="margin-left:5px" v-decorator="['nationality']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="民族" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['nation']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="身高" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['height']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="体重" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['weight']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="户口" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['account']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="婚姻状况" v-bind="formItemLayout">
            <a-form-item label="状态" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'maritalStatus',
                {rules: [{ required: true, message: '请选择状态' }]}
              ]"
            >
              <a-radio value="0">锁定</a-radio>
              <a-radio value="1">有效</a-radio>
            </a-radio-group>
          </a-form-item>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="籍贯" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['nativePlace']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="现居住地" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['currentAddress']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="现任公司" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['companyName']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="职位" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['positionName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="目前薪资" v-bind="formItemLayout">
             <a-input style="margin-left:5px" v-decorator="['currentSalary']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="任职年限" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['officeTerm']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="自我评价" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'selfAssessment',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

       <a-row>
        <a-col :md="24">
          <a-form-item label="工作经历" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'workExperience',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="项目经验" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'projectExperience',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="教育经历" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'educationRecord',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="培训经历" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'trainRecord',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="语言能力" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'languageSkill',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>


      <a-row>
        <a-col :md="24">
          <a-form-item label="计算机" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'computer',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="相关技能" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'relatedSkill',
              {rules: [
                { max: 100, message: '长度不能超过100个字符'}
              ]}]">
            </a-textarea>
          </a-form-item>
        </a-col>
      </a-row>

       <a-row>
        <a-col :md="24">
          <a-form-item label="备注" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'content',
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
  name: "PersonEdit",
  props: {
    personEditVisiable: {
      default: false
    }
  },
  data() {
    return {
      formItemLayout,
      dateFormat: 'YYYY/MM/DD',
      form: this.$form.createForm(this),
      personId: "",
      loading: false,
      birthday:'',
      personName:'',
      person: {},
      graduateTime: '',
    };
  },
  // computed: {
  //   ...mapState({
  //     currentperson: state => state.account.person
  //   })
  // },
  methods: {
    // ...mapMutations({
    //   setperson: "account/setperson"
    // }),
    moment,
    onClose() {
      this.loading = false;
      this.form.resetFields();
      this.$emit("close");
    },
    setFormValues({ ...person }) {
      this.personId = person.personId;
      this.birthday = person.birthday;
      this.graduateTime = person.graduateTime;
      let fields = ["personName", "personSex", "personSort",
       "personLevel", "birthday","workYears","graduateTime",
       "graduateInstitutions","major","educationBackground",
       "email","mobilePhone","workPhone","familyPhone","identityCard",
       "nationality","nation","height","weight","account","maritalStatus",
       "nativePlace","currentAddress","companyName","positionName","currentSalary",
       "officeTerm","selfAssessment","workExperience","projectExperience","educationRecord",
       "trainRecord","languageSkill","computer","relatedSkill","content"];
      Object.keys(person).forEach(key => {
        if (fields.indexOf(key) !== -1) {
          this.form.getFieldDecorator(key);
          let obj = {};
          obj[key] = person[key];
          this.form.setFieldsValue(obj);
        }
      });
    },
    onBirthdayChange(dates, dateStrings) {
      this.birthday = dateStrings
    },
    onGraduateTimeChange(dates, dateStrings) {
      this.graduateTime = dateStrings
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        if (!err) {
          this.loading = true;
          let person = this.form.getFieldsValue();
          person.personId = this.personId;
          person.birthday = this.birthday;
          person.graduateTime = this.graduateTime;
          this.$put("person", {
            ...person
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
