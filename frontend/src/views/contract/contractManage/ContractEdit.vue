<template>
  <a-drawer
    title="修改合同信息"
    :maskClosable="false"
    width="1450"
    placement="right"
    :closable="false"
    @close="onClose"
    :visible="contractEditVisiable"
    style="height: calc(100% - 55px);overflow: auto;padding-bottom: 53px;"
  >
    <a-form :form="form">
      <a-row>
        <a-col :md="8">
          <a-form-item label="合同名称:" v-bind="formItemLayout">
            <a-input style="margin-left:5px" readonly v-decorator="['contractName']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="合同编号" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['contractCode']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="支付方式" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['payWay']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
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
          <a-form-item label="完成日期" v-bind="formItemLayout">
              <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.finishTime, dateFormat)"
                    :getPopupContainer="trigger => trigger.parentNode"
                    @change="onFinishTimeChange"
                  />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="开票日期" v-bind="formItemLayout">
              <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.makeInvoiceTime, dateFormat)"
                    :getPopupContainer="trigger => trigger.parentNode"
                    @change="onMakeInvoiceTimeChange"
                  />
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="公司签约人" v-bind="formItemLayout">
             <a-input style="margin-left:5px" v-decorator="['companySigned']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="客户签约人" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['customerSigned']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="签约时间" v-bind="formItemLayout">
            <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.signedTime, dateFormat)"
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
          <a-form-item label="归档日期" v-bind="formItemLayout">
            <a-date-picker style="margin-left:5px"
                    :format="dateFormat"
                    :defaultValue="moment(this.fillTime, dateFormat)"
                    :getPopupContainer="trigger => trigger.parentNode"
                    @change="onFillTimeChange"
                  />
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="金额" v-bind="formItemLayout">
            <a-input style="margin-left:5px" v-decorator="['money']"/>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row>
        <a-col :md="8">
          <a-form-item label="执行状态" v-bind="formItemLayout">
             <a-input style="margin-left:5px" v-decorator="['executeStatus']"/>
          </a-form-item>
        </a-col>
        <a-col :md="8">
          <a-form-item label="合同内容" v-bind="formItemLayout">
              <a-input style="margin-left:5px" v-decorator="['contractInfo']"/>
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
          <a-form-item label="备注" v-bind="formItemLayout">
            <a-textarea
              :rows="4"
              v-decorator="[
              'info',
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
  name: "ContractEdit",
  props: {
    contractEditVisiable: {
      default: false
    }
  },
  data() {
    return {
      formItemLayout,
      dateFormat: 'YYYY/MM/DD',
      form: this.$form.createForm(this),
      contractId: "",
      loading: false,
      startTime:'',
      finishTime:'',
      makeInvoiceTime:'',
      signedTime:'',
      fillTime:'',
      contractName:'',
      contract: {},
    };
  },
  // computed: {
  //   ...mapState({
  //     currentcontract: state => state.account.contract
  //   })
  // },
  methods: {
    // ...mapMutations({
    //   setcontract: "account/setcontract"
    // }),
    moment,
    onClose() {
      this.loading = false;
      this.form.resetFields();
      this.$emit("close");
    },
    setFormValues({ ...contract }) {
      this.contractId = contract.contractId;
      this.startTime = contract.startTime;
      this.finishTime = contract.finishTime;
      this.makeInvoiceTime = contract.makeInvoiceTime;
      this.signedTime = contract.signedTime;
      this.fillTime = contract.fillTime;
      let fields = ["contractName", "contractCode", "startTime",
       "finishTime", "makeInvoiceTime","payWay","companySigned",
       "customerSigned","signedTime","signedAddress",
       "storeAddress","fillTime","money","executeStatus","contractInfo",
       "orderNumber","info"];
      Object.keys(contract).forEach(key => {
        if (fields.indexOf(key) !== -1) {
          this.form.getFieldDecorator(key);
          let obj = {};
          obj[key] = contract[key];
          this.form.setFieldsValue(obj);
        }
      });
    },
    onStartTimeChange(dates, dateStrings) {
      this.startTime = dateStrings
    },
    onFinishTimeChange(dates, dateStrings) {
      this.finishTime = dateStrings
    },
    onMakeInvoiceTimeChange(dates, dateStrings) {
      this.makeInvoiceTime = dateStrings
    },
    onSignedTimeChange(dates, dateStrings) {
      this.finishTime = dateStrings
    },
    onFillTimeChange(dates, dateStrings) {
      this.fillTime = dateStrings
    },
    handleSubmit() {
      this.form.validateFields((err, values) => {
        if (!err) {
          this.loading = true;
          let contract = this.form.getFieldsValue();
          contract.contractId = this.contractId;
          this.$put("contract", {
            ...contract
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
