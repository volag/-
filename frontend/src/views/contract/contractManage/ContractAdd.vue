<template>
  <a-drawer
    title="新增合同"
    :maskClosable="false"
    width="1450"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="contractAddVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label="合同名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['contractName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="支付方式" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="contractLevel"
            ></a-tree-select>-->
            <a-input style="margin-left:5px" v-decorator="['payWay']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="公司签约人" v-bind="formItemLayout">
            <!-- <a-tree-select
              :allowClear="true"
              :dropdownStyle="{ maxHeight: '220px', overflow: 'auto' }"
              :treeData="deptTreeData"
              @change="onDeptChange"
              :value="contractLevel"
            ></a-tree-select>-->
            <a-input style="margin-left:5px" v-decorator="['companySigned']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="合同编号" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['contractCode']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户签约人" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['customerSigned']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="签约时间" v-bind="formItemLayout">
            <a-date-picker
              style="margin-left:5px"
              :format="dateFormat"
              :getPopupContainer="trigger => trigger.parentNode"
              @change="onSignedTimeChange"
            />
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
              @change="onFinishTimeChange"
            />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="开票日期" v-bind="formItemLayout">
            <a-date-picker
              style="margin-left:5px"
              :format="dateFormat"
              :getPopupContainer="trigger => trigger.parentNode"
              @change="onMakeInvoiceTimeChange"
            />
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="签约地点" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['signedAddress']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="存放地点" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['storeAddress']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="金额:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['money']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="执行状态" v-bind="formItemLayout">
            <a-radio-group
              v-decorator="[
                'executeStatus',
                {rules: [{ required: true, message: '请选择状态' }]}
              ]"
            >
              <a-radio value="0">待执行</a-radio>
              <a-radio value="1">已执行</a-radio>
            </a-radio-group>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="合同内容" v-bind="formItemLayout">
            <a-input  v-decorator="['contractInfo']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="对应订单" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['orderNumber']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="24">
          <a-form-item label="备注" style="margin-left:-150px" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'info',
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
  name: "ContractAdd",
  props: {
    contractAddVisiable: {
      default: false
    }
  },
  data() {
    return {
      dateFormat: "YYYY/MM/DD",
      loading: false,
      formItemLayout,
      form: this.$form.createForm(this),
      contract: {},
      finishTime: "",
      startTime: "",
      makeInvoiceTime: '',
      signedTime: ''
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
    onSignedTimeChange(dates, dateStrings) {
      this.signedTime = dateStrings;
    },
    onMakeInvoiceTimeChange(dates, dateStrings) {
      this.makeInvoiceTime = dateStrings;
    },
    onFinishTimeChange(dates, dateStrings) {
      this.finishTime = dateStrings;
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        this.contract = Object.assign(values);
        this.contract.startTime = this.startTime;
        this.contract.makeInvoiceTime = this.makeInvoiceTime;
        this.contract.signedTime = this.signedTime;
        this.contract.userId = this.user.userId;
        this.contract.finishTime = this.finishTime;
        console.log(this.contract);
        if (!err) {
          this.$post("contract", {
            ...this.contract
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
