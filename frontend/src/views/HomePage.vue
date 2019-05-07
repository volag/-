<template>
  <div :class="[multipage === true ? 'multi-page':'single-page', 'not-menu-page', 'home-page']">
    <a-row :gutter="8" class="head-info">
      <a-card class="head-info-card">
        <a-col :span="12">
          <div class="head-info-avatar">
            <img alt="头像" :src="avatar">
          </div>
          <div class="head-info-count">
            <div class="head-info-welcome">{{welcomeMessage}}</div>

            <!-- <div class="head-info-welcome">{{address}}</div> -->
            <div class="head-info-desc">
              <p>{{user.companyName ? user.companyName : '暂无公司'}} | {{user.roleName ? user.roleName : '暂无角色'}}</p>
            </div>
            <div
              class="head-info-time"
            >上次登录时间：{{user.lastLoginTime ? user.lastLoginTime : '第一次访问系统'}}</div>
            <div class="head-info-time">当前地址：{{address}}</div>
          </div>
        </a-col>
        <a-col :span="12">
          <div>
            <a-row class="more-info">
              <a-col :span="4"></a-col>
              <a-col :span="4"></a-col>
              <a-col :span="4"></a-col>
              <a-col :span="4">
                <head-info title="今日IP" :content="todayIp" :center="false" :bordered="false"/>
              </a-col>
              <a-col :span="4">
                <head-info
                  title="今日访问"
                  :content="todayVisitCount"
                  :center="false"
                  :bordered="false"
                />
              </a-col>
              <a-col :span="4">
                <head-info title="总访问量" :content="totalVisitCount" :center="false"/>
              </a-col>
            </a-row>
          </div>
        </a-col>
      </a-card>
    </a-row>
    <a-row :gutter="8" class="count-info">
      <a-col :span="12" class="visit-count-wrapper">
        <a-card title="近七日访问记录" class="visit-count">
          <apexchart ref="count" type="bar" height="300" :options="chartOptions" :series="series"/>
        </a-card>
      </a-col>
      <!-- <a-col :span="12" class="project-wrapper"> -->
      <a-col :span="12" class="visit-count-wrapper">
        <a-card title="项目跟踪" class="project-card">
          <apexchart
            style="margin-left:100px"
            ref="project"
            type="pie"
            width="380"
            :options="chartOptions1"
            :series="series1"
          />
          <!-- <a
            href="https://github.com/wuyouzhuguli?tab=repositories"
            target="_blank"
            slot="extra"
          >所有项目</a>
          <table>
            <tr>
              <td>
                <div class="project-avatar-wrapper">
                  <a-avatar class="project-avatar">{{projects[0].avatar}}</a-avatar>
                </div>
                <div class="project-detail">
                  <div class="project-name">{{projects[0].name}}</div>
                  <div class="project-desc">
                    <p>{{projects[0].des}}</p>
                  </div>
                </div>
              </td>
              <td>
                <div class="project-avatar-wrapper">
                  <a-avatar class="project-avatar">{{projects[1].avatar}}</a-avatar>
                </div>
                <div class="project-detail">
                  <div class="project-name">{{projects[1].name}}</div>
                  <div class="project-desc">
                    <p>{{projects[1].des}}</p>
                  </div>
                </div>
              </td>
            </tr>
            <tr>
              <td>
                <div class="project-avatar-wrapper">
                  <a-avatar class="project-avatar">{{projects[2].avatar}}</a-avatar>
                </div>
                <div class="project-detail">
                  <div class="project-name">{{projects[2].name}}</div>
                  <div class="project-desc">
                    <p>{{projects[2].des}}</p>
                  </div>
                </div>
              </td>
              <td>
                <div class="project-avatar-wrapper">
                  <a-avatar class="project-avatar">{{projects[3].avatar}}</a-avatar>
                </div>
                <div class="project-detail">
                  <div class="project-name">{{projects[3].name}}</div>
                  <div class="project-desc">
                    <p>{{projects[3].des}}</p>
                  </div>
                </div>
              </td>
            </tr>
            <tr>
              <td>
                <div class="project-avatar-wrapper">
                  <a-avatar class="project-avatar">{{projects[4].avatar}}</a-avatar>
                </div>
                <div class="project-detail">
                  <div class="project-name">{{projects[4].name}}</div>
                  <div class="project-desc">
                    <p>{{projects[4].des}}</p>
                  </div>
                </div>
              </td>
              <td></td>
            </tr>
          </table>-->
        </a-card>
      </a-col>
    </a-row>
  </div>
</template>
<script>
import HeadInfo from "@/views/common/HeadInfo";
import { mapState } from "vuex";
import moment from "moment";
import pdf from "vue-pdf";
import store from '../store';
moment.locale("zh-cn");

export default {
  name: "HomePage",
  components: { HeadInfo, pdf },
  data() {
    return {
      series1: [],
      chartOptions1: {
        labels: ["进行中", "已完成", "暂停", "终止", "未开始"],
        responsive: [
          {
            breakpoint: 480,
            options: {
              chart: {
                width: 200
              },
              legend: {
                position: "bottom"
              }
            }
          }
        ]
      },
      series: [],
      chartOptions: {
        chart: {
          toolbar: {
            show: false
          }
        },
        plotOptions: {
          bar: {
            horizontal: false,
            columnWidth: "35%"
          }
        },
        dataLabels: {
          enabled: false
        },
        stroke: {
          show: true,
          width: 2,
          colors: ["transparent"]
        },
        xaxis: {
          categories: []
        },
        fill: {
          opacity: 1
        }
      },
      projects: [
        {
          name: "FEBS-Shiro",
          des: "Spring Boot 2.0.4 & Shiro1.4.0 权限管理系统。",
          avatar: "F"
        },
        {
          name: "FEBS-Security",
          des: "Spring Boot 2.0.4 & Spring Security 5.0.7 权限管理系统。",
          avatar: "F"
        },
        {
          name: "SpringAll",
          des: "循序渐进学习Spring Boot、Spring Cloud与Spring Security。",
          avatar: "S"
        },
        {
          name: "FEBS-Shiro-Vue",
          des: "FEBS-Shiro前后端分离版本，前端架构采用Vue全家桶。",
          avatar: "F"
        },
        {
          name: "FEBS-Actuator",
          des: "使用Spring Boot Admin 2.0.2构建，用于监控FEBS。",
          avatar: "F"
        }
      ],
      todayIp: "",
      todayVisitCount: "",
      totalVisitCount: "",
      userRole: "",
      lastLoginTime: "",
      welcomeMessage: "",
      project: {},
      projectList: [],
    };
  },
  computed: {
    ...mapState({
      multipage: state => state.setting.multipage,
      user: state => state.account.user,
      // address: state => state.account.address
    }),
    avatar() {
      return `http://pqggd9642.bkt.clouddn.com/${this.user.avatar}`;
    },
    address(){
      return store.state.account.address;
      // return this.address;
    }
  },
  methods: {
    welcome() {
      const date = new Date();
      const hour = date.getHours();
      let time =
        hour < 6
          ? "早上好"
          : hour <= 11
          ? "上午好"
          : hour <= 13
          ? "中午好"
          : hour <= 18
          ? "下午好"
          : "晚上好";
      let welcomeArr = [
        "喝杯咖啡休息下吧☕",
        "要不要和朋友打局LOL",
        "要不要和朋友打局王者荣耀",
        "几天没见又更好看了呢😍",
        "今天又写了几个Bug🐞呢",
        "今天在群里吹水了吗",
        "今天吃了什么好吃的呢",
        "今天您微笑了吗😊",
        "今天帮助别人解决问题了吗",
        "准备吃些什么呢",
        "周末要不要去看电影？"
      ];
      let index = Math.floor(Math.random() * welcomeArr.length);
      return `${time}，${this.user.username}，${welcomeArr[index]}`;
    }
  },
  mounted() {
    this.welcomeMessage = this.welcome();
    this.project.userId = this.user.userId;
    this.$get(`project`,{...this.project})
      .then(r => {
        this.projects = r.data.rows; 
        let projectCount = [];
        let count1 = 0;
        let count2 = 0;
        let count3 = 0;
        let count4 = 0;
        let count5 = 0;
        for (let o of this.projects) {
          switch (o.projectStatus) {
          case "0":
            count1++;
            break;
          case "1":
            count2++;
            break;
          case "2":
            count3++;
            break;
          case "3":
            count4++
            break;
          case "4":
            count5++;
            break;
          default:
            ;
          }
        }
        projectCount.push(count1);
        projectCount.push(count2);
        projectCount.push(count3);
        projectCount.push(count4);
        projectCount.push(count5);
        this.series1 = projectCount;
     })
      .catch(r => {
        console.error(r);
        this.$message.error("获取首页信息失败");
      });
    this.$get(`index/${this.user.username}`)
      .then(r => {
        let data = r.data.data;
        this.todayIp = data.todayIp;
        this.todayVisitCount = data.todayVisitCount;
        this.totalVisitCount = data.totalVisitCount;
        let sevenVisitCount = [];
        let dateArr = [];
        for (let i = 6; i >= 0; i--) {
          let time = moment()
            .subtract(i, "days")
            .format("MM-DD");
          let contain = false;
          for (let o of data.lastSevenVisitCount) {
            if (o.days === time) {
              contain = true;
              sevenVisitCount.push(o.count);
            }
          }
          if (!contain) {
            sevenVisitCount.push(0);
          }
          dateArr.push(time);
        }
        let sevenUserVistCount = [];
        for (let i = 6; i >= 0; i--) {
          let time = moment()
            .subtract(i, "days")
            .format("MM-DD");
          let contain = false;
          for (let o of data.lastSevenUserVisitCount) {
            if (o.days === time) {
              contain = true;
              sevenUserVistCount.push(o.count);
            }
          }
          if (!contain) {
            sevenUserVistCount.push(0);
          }
        }
        this.$refs.count.updateSeries(
          [
            {
              name: "您",
              data: sevenUserVistCount
            },
            {
              name: "总数",
              data: sevenVisitCount
            }
          ],
          true
        );
        this.$refs.count.updateOptions(
          {
            xaxis: {
              categories: dateArr
            },
            // title: {
            //   text: "近七日系统访问记录",
            //   align: "left"
            // }
          },
          true,
          true
        );
      })
      .catch(r => {
        console.error(r);
        this.$message.error("获取首页信息失败");
      });
  }
};
</script>
<style lang="less">
.home-page {
  .head-info {
    margin-bottom: 0.5rem;
    .head-info-card {
      padding: 0.5rem;
      border-color: #f1f1f1;
      .head-info-avatar {
        display: inline-block;
        float: left;
        margin-right: 1rem;
        img {
          width: 5rem;
          border-radius: 2px;
        }
      }
      .head-info-count {
        display: inline-block;
        float: left;
        .head-info-welcome {
          font-size: 1.05rem;
          margin-bottom: 0.1rem;
        }
        .head-info-desc {
          color: rgba(0, 0, 0, 0.45);
          font-size: 0.8rem;
          padding: 0.2rem 0;
          p {
            margin-bottom: 0;
          }
        }
        .head-info-time {
          color: rgba(0, 0, 0, 0.45);
          font-size: 0.8rem;
          padding: 0.2rem 0;
        }
      }
    }
  }
  .count-info {
    .visit-count-wrapper {
      padding-left: 0 !important;
      .visit-count {
        // padding: 0.5rem;
        border-color: #f1f1f1;
        .ant-card-body {
          padding: 0.5rem 1rem !important;
        }
      }
    }
    .project-wrapper {
      padding-right: 0 !important;
      .project-card {
        border: none !important;
        .ant-card-head {
          border-left: 1px solid #f1f1f1 !important;
          border-top: 1px solid #f1f1f1 !important;
          border-right: 1px solid #f1f1f1 !important;
        }
        .ant-card-body {
          padding: 0 !important;
          table {
            width: 100%;
            td {
              width: 50%;
              border: 1px solid #f1f1f1;
              padding: 0.6rem;
              .project-avatar-wrapper {
                display: inline-block;
                float: left;
                margin-right: 0.7rem;
                .project-avatar {
                  color: #42b983;
                  background-color: #d6f8b8;
                }
              }
            }
          }
        }
        .project-detail {
          display: inline-block;
          float: left;
          text-align: left;
          width: 78%;
          .project-name {
            font-size: 0.9rem;
            margin-top: -2px;
            font-weight: 600;
          }
          .project-desc {
            color: rgba(0, 0, 0, 0.45);
            p {
              margin-bottom: 0;
              font-size: 0.6rem;
              white-space: normal;
            }
          }
        }
      }
    }
  }
}
</style>
