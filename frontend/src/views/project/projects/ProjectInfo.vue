<template>
  <a-modal
    v-model="show"
    :centered="true"
    :keyboard="false"
    :footer="null"
    :width="900"
    @cancel="handleCancleClick"
    title="项目信息">
    <a-layout class="project-info">
      <a-layout-content class="project-content-one">
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type="user"/>项目名称:{{projectInfoData.projectName}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="user"/>项目编号:{{projectInfoData.projectCode}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="user"/>项目状态:{{projectInfoData.projectStatus}}</a-col>
            <!-- <a-col :md="8" :sm="48" >
              <a-icon type="smile" v-if="projectInfoData.maritalStatus === '1'"/>
              <a-icon type="frown" v-else/>性别：
              <template v-if="projectInfoData.maritalStatus === '0'">
                <a-tag color="red">男</a-tag>
              </template>
              <template v-else-if="projectInfoData.maritalStatus === '1'">
                <a-tag color="cyan">女</a-tag>
              </template>
              <template v-else>
                {{projectInfoData.maritalStatus}}
              </template></a-col>
              <a-col :md="8" :sm="48" >
              <a-icon type="smile" v-if="projectInfoData.maritalStatus === '1'"/>
              <a-icon type="frown" v-else/>婚姻状况
              <template v-if="projectInfoData.maritalStatus === '0'">
                <a-tag color="red">已婚</a-tag>
              </template>
              <template v-else-if="projectInfoData.maritalStatus === '1'">
                <a-tag color="cyan">未婚</a-tag>
              </template>
              <template v-else>
                {{projectInfoData.maritalStatus}}
              </template>
            </a-col> -->
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>优先级别:{{projectInfoData.priorityLevel}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>开始日期:{{startTime}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>计划完成日期:{{planFinishTime}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>客户名称:{{projectInfoData.companyName}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>收款状态:{{projectInfoData.gatherStatus}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>客户经理:{{projectInfoData.customerManager}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>业务人员:{{projectInfoData.businessPerson}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="mail"/>项目主管:{{projectInfoData.projectCharge}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="mobile"/>项目顾问:{{projectInfoData.projectCounselor}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type="phone"/>客户联系人:{{projectInfoData.companyContact}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="contacts"/>寻访员:{{projectInfoData.searchPerson}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="idcard"/>客户付款人:{{projectInfoData.companyPayer}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>汇报对象:{{projectInfoData.reportObject}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>下属职位:{{projectInfoData.subordinatePosition}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="home"/>工作地点:{{projectInfoData.workAddress}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>招聘人数:{{projectInfoData.number}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>行业:{{projectInfoData.trade}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>适合职能:{{projectInfoData.suitableFunction}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>外语要求:{{projectInfoData.languageRequire}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>性别要求:{{projectInfoData.sexRequire}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>职位级别:{{projectInfoData.positionLevel}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type=""/>职位类型:{{projectInfoData.positionSort}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>年龄要求:{{projectInfoData.ageRequire}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type=""/>薪资范围:{{projectInfoData.positionInfo}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="24" :sm="48" ><a-icon type=""/>公司简介:{{projectInfoData.companyInfo}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="24" :sm="48" ><a-icon type=""/>职位开发原因:{{projectInfoData.workExperience}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="24" :sm="48" ><a-icon type="fork"/>职位描述:{{projectInfoData.projectExperience}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="24" :sm="48" ><a-icon type="book"/>委托前进展:{{projectInfoData.educationRecord}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="24" :sm="48" ><a-icon type="team"/>执行分析:{{projectInfoData.trainRecord}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="24" :sm="48" ><a-icon type="message"/>福利:{{projectInfoData.welfare}}</a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
            <a-col :md="8" :sm="48" ><a-icon type="user"/>创建人:{{projectInfoData.userId}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="clock-circle"/>创建时间：{{projectInfoData.createTime}}</a-col>
            <a-col :md="8" :sm="48" ><a-icon type="file-pdf"/>简历路径:{{projectInfoData.resume}}</a-col>
        </a-row>
      </a-layout-content>
    </a-layout>
  </a-modal>
</template>
<script>
import moment from "moment"

export default {
  name: 'ProjectInfo',
  props: {
    projectInfoVisiable: {
      require: true,
      default: false
    },
    projectInfoData: {
      require: true
    }
  },
  computed: {
    show: {
      get: function () {
        return this.projectInfoVisiable
      },
      set: function () {
      }
    },
    sex () {
      switch (this.projectInfoData.projectSex) {
        case '0':
          return '男'
        case '1':
          return '女'
        case '2':
          return '保密'
        default:
          return this.projectInfoData.projectSex
      }
    },
    startTime(){
      return moment(this.projectInfoData.startTime).format('YYYY-MM-DD')
    },
    planFinishTime(){
      return moment(this.projectInfoData.planFinishTime).format('YYYY-MM-DD')
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
@import "projectInfo";
</style>
