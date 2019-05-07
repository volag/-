<template>
  <a-modal
    v-model="show"
    :centered="true"
    :keyboard="false"
    :footer="null"
    :width="900"
    @cancel="handleCancleClick"
    title="人才信息"
  >
    <a-layout class="person-info">
      <a-layout-content class="person-content-one">
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type="user"/>
            姓名：{{personInfoData.personName}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="smile" v-if="personInfoData.maritalStatus === '1'"/>
            <a-icon type="frown" v-else/>性别：
            <template v-if="personInfoData.maritalStatus === '0'">
              <a-tag color="red">男</a-tag>
            </template>
            <template v-else-if="personInfoData.maritalStatus === '1'">
              <a-tag color="cyan">女</a-tag>
            </template>
            <template v-else>{{personInfoData.maritalStatus}}</template>
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="smile" v-if="personInfoData.maritalStatus === '1'"/>
            <a-icon type="frown" v-else/>婚姻状况
            <template v-if="personInfoData.maritalStatus === '0'">
              <a-tag color="red">已婚</a-tag>
            </template>
            <template v-else-if="personInfoData.maritalStatus === '1'">
              <a-tag color="cyan">未婚</a-tag>
            </template>
            <template v-else>{{personInfoData.maritalStatus}}</template>
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            级别:{{personInfoData.personLevel}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            出生日期:{{birthday}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            工作年限:{{personInfoData.workYears}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            毕业时间:{{graduateTime}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            毕业院校:{{personInfoData.graduateInstitutions}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            专业:{{personInfoData.major}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            学历:{{personInfoData.educationBackground}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="mail"/>
            电子邮件:{{personInfoData.email}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="mobile"/>
            手机号码:{{personInfoData.mobilePhone}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type="phone"/>
            办公电话:{{personInfoData.workPhone}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="contacts"/>
            家庭电话:{{personInfoData.familyPhone}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="idcard"/>
            身份证号:{{personInfoData.identityCard}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            国籍:{{personInfoData.nationality}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            民族:{{personInfoData.nation}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="home"/>
            现在居住地:{{personInfoData.currentAddress}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            身高:{{personInfoData.height}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            体重:{{personInfoData.weight}}kg
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            户口:{{personInfoData.account}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            类别:{{personInfoData.personSort}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            籍贯:{{personInfoData.nativePlace}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            现任公司:{{personInfoData.companyName}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            任职年限:{{personInfoData.officeTerm}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            职位:{{personInfoData.positionName}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            目前薪资:{{personInfoData.currentSalary}}k
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type/>
            自我评价:{{personInfoData.selfAssessment}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type/>
            工作经历:{{personInfoData.workExperience}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type="fork"/>
            项目经验:{{personInfoData.projectExperience}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type="book"/>
            教育经历:{{personInfoData.educationRecord}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type="team"/>
            培训经历:{{personInfoData.trainRecord}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type/>
            语言能力:{{personInfoData.languageSkill}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px;">
          <a-col :md="24" :sm="48">
            <a-icon type="desktop"/>
            计算机:{{personInfoData.computer}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type/>
            相关技能:{{personInfoData.relatedSkill}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type="message"/>
            备注:{{personInfoData.content}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type="user"/>
            创建人:{{personInfoData.userId}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="clock-circle"/>
            创建时间：{{personInfoData.createTime}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type="file-pdf"/>
            简历路径:{{personInfoData.resume}}
          </a-col>
        </a-row>
      </a-layout-content>
    </a-layout>
  </a-modal>
</template>
<script>
import moment from "moment";

export default {
  name: "PersonInfo",
  props: {
    personInfoVisiable: {
      require: true,
      default: false
    },
    personInfoData: {
      require: true
    }
  },
  computed: {
    show: {
      get: function() {
        return this.personInfoVisiable;
      },
      set: function() {}
    },
    sex() {
      switch (this.personInfoData.personSex) {
        case "0":
          return "男";
        case "1":
          return "女";
        case "2":
          return "保密";
        default:
          return this.personInfoData.personSex;
      }
    },
    birthday() {
      return moment(this.personInfoData.birthday).format("YYYY-MM-DD");
    },
    graduateTime() {
      return moment(this.personInfoData.graduateTime).format("YYYY-MM-DD");
    }
  },
  methods: {
    handleCancleClick() {
      this.$emit("close");
    }
  }
};
</script>
<style lang="less" scoped>
@import "PersonInfo";
</style>
