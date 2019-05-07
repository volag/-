<template>
  <a-modal
    v-model="show"
    :centered="true"
    :keyboard="false"
    :footer="null"
    :width="900"
    @cancel="handleCancleClick"
    title="合同信息">
    <a-layout class="contract-info">
      <a-layout-content class="contract-content-one">
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type="user"/>姓名：{{contractInfoData.contractName}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>合同编号:{{contractInfoData.contractCode}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>支付方式:{{contractInfoData.payWay}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>开始日期:{{startTime}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>完成日期:{{finishTime}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>开票日期:{{makeInvoiceTime}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>公司签约人:{{contractInfoData.companySigned}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>客户签约人:{{contractInfoData.customerSigned}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>签约时间:{{signedTime}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>签约地点:{{contractInfoData.signedAddress}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="mail"/>存放地点:{{contractInfoData.storeAddress}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="mobile"/>归档日期:{{fillTime}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type="phone"/>金额:{{contractInfoData.money}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="contacts"/>执行状态:{{contractInfoData.executeStatus}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="idcard"/>合同内容:{{contractInfoData.contractInfo}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>对应订单:{{contractInfoData.orderNumber}}</a-col>
        </a-row>

        <a-row style="margin-bottom:15px">
            <a-col :md="24" :sm="48" ><a-icon type="message"/>备注:{{contractInfoData.info}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="12" :sm="48" ><a-icon type="user"/>创建人:{{contractInfoData.userId}}</a-col>
            <a-col :md="12" :sm="48" ><a-icon type="clock-circle"/>创建时间：{{contractInfoData.createTime}}</a-col>
        </a-row>
      </a-layout-content>
    </a-layout>
  </a-modal>
</template>
<script>
import moment from "moment"

export default {
  name: 'ContractInfo',
  props: {
    contractInfoVisiable: {
      require: true,
      default: false
    },
    contractInfoData: {
      require: true
    }
  },
  computed: {
    show: {
      get: function () {
        return this.contractInfoVisiable
      },
      set: function () {
      }
    },
    // sex () {
    //   switch (this.contractInfoData.contractSex) {
    //     case '0':
    //       return '男'
    //     case '1':
    //       return '女'
    //     case '2':
    //       return '保密'
    //     default:
    //       return this.contractInfoData.contractSex
    //   }
    // },
    startTime(){
      return moment(this.contractInfoData.startTime).format('YYYY-MM-DD')
    },
    finishTime(){
      return moment(this.contractInfoData.finishTime).format('YYYY-MM-DD')
    },
    makeInvoiceTime(){
      return moment(this.contractInfoData.makeInvoiceTime).format('YYYY-MM-DD')
    },
    signedTime(){
      return moment(this.contractInfoData.signedTime).format('YYYY-MM-DD')
    },
    fillTime(){
      return moment(this.contractInfoData.fillTime).format('YYYY-MM-DD')
    }
  },
  methods: {
    handleCancleClick () {
      this.$emit('close')
    }
  }
}
</script>
<style lang="less" scoped>
@import "ContractInfo";
</style>
