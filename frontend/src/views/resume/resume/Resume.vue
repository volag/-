<template>
  <div :class="resume-resume">
    <a-card title="个人简历" style="margin-left:250px;width: 800px">
      <a-card title="基本资料">
        <!-- <a href="javascript:void(0)" slot="extra" @click="updateresume">修改资料</a> -->
        <a-row :gutter="12">
          <a-col :span="12">
            <a-row style="text-align: center">
              <img style="width: 165px;height:160px;border-radius: 2px," alt="头像" :src="photo">
            </a-row>
            <a-row style="text-align: center;margin-top:10px">
              <a-upload
                :remove="handleRemove"
                :disabled="fileList.length === 1"
                :beforeUpload="beforeUpload"
                :fileList="fileList"
              >
                <a-button>选择头像</a-button>
              </a-upload>
              <a-button
                style="margin-top:5px"
                @click="handleUploadAvatar"
                :disabled="fileList.length === 0"
                :loading="uploading"
              >
                <a-icon type="upload"/>
                {{uploading ? '上传中' : '上传头像' }}
              </a-button>
            </a-row>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>出生日期：{{birthday}}</p>
            <p>性别：{{sex}}</p>
            <p>电话：{{user.mobile ? user.mobile : '暂未绑定电话'}}</p>
            <p>邮箱：{{user.email ? user.email : '暂未绑定邮箱'}}</p>
            <p>公司：{{user.companyName? user.companyName: '暂无公司'}}</p>
            <p>地址：{{resume.userAddress}}</p>
            <p>描述：{{user.description}}</p>
          </a-col>
        </a-row>
      </a-card>
      <a-card title="简历信息">
        <a href="javascript:void(0)" slot="extra" @click="resumeEdit">修改资料</a>
        <!-- @click="updateResumeIntention" -->
        <a-row>
          <a-col :span="12" style="font-size: 1rem">
            <p>期望行业：{{resume.trade}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>期望职能：{{resume.functional}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>期望地点：{{resume.workAddress}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>期望年薪：{{resume.annualSalary}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>目前年薪：{{resume.currentSalary}}</p>
          </a-col>
        </a-row>
      </a-card>
      <a-card title="我的标签">
        <a-row :gutter="16">
          <a-col :span="12" style="font-size: 1rem">
            <a-checkable-tag>{{resume.labelName}}</a-checkable-tag>
          </a-col>
        </a-row>
      </a-card>
      <a-card title="工作经历">
        <a-row>
          <a-col :span="12" style="font-size: 1rem">
            <p>公司名称：{{resume.companyName}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>公司行业：{{resume.companyTrade}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>公司性质：{{resume.companyNature}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>公司规模：{{resume.companyScale}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>所在部门：{{resume.department}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>职位名称：{{resume.positionName}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>工作地点：{{resume.companyAddress}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>下属人数：{{resume.underNumber}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>任职时间从：{{resume.employmentPeriodFrom}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>任职时间到：{{resume.employmentPeriodTo}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>工作地点：{{resume.companyAddress}}</p>
          </a-col>
        </a-row>
        <a-row style="margin-top:10px">
          <a-col :span="3" style="font-size: 1rem">
            <p>职责业绩：</p>
          </a-col>
          <a-col :span="21" style="font-size: 1rem">
            <a-textarea
              :autosize="{ minRows: 4, maxRows: 12 }"
              :placeholder="resume.jobPerformance"
            />
          </a-col>
        </a-row>
        <a-row style="margin-top:10px">
          <a-col :span="3" style="font-size: 1rem">
            <p>公司简介：</p>
          </a-col>
          <a-col :span="21" style="font-size: 1rem">
            <a-textarea :autosize="{ minRows: 4, maxRows: 12 }" :placeholder="resume.companyIntro"/>
          </a-col>
        </a-row>
      </a-card>
      <a-card title="教育经历">
        <a-row>
          <a-col :span="12" style="font-size: 1rem">
            <p>学校名称：{{resume.schoolName}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>专业名称：{{resume.majorName}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>就读时间从：{{resume.timeStart}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>就读时间到：{{resume.timeEnd}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>学历：{{resume.education}}</p>
          </a-col>
          <!-- <a-col :span="12" style="font-size: 1rem">
            <p>是否统招：{{resume.recruitmentFlag}}</p>
          </a-col>-->
          <a-col :span="12" style="font-size: 1rem">
            <a-icon type="smile" v-if="resume.recruitmentFlag === 'Y'"/>
            <a-icon type="frown" v-else/>是否统招：
            <template v-if="resume.recruitmentFlag === 'N'">
              <a-tag color="red">否</a-tag>
            </template>
            <template v-else-if="resume.recruitmentFlag === 'Y'">
              <a-tag color="cyan">是</a-tag>
            </template>
          </a-col>
        </a-row>
      </a-card>
      <a-card title="语言能力">
        <a-row :gutter="16">
          <a-col :span="12" style="font-size: 1rem">
            <a-checkable-tag>{{resume.language}}</a-checkable-tag>
            <a-checkable-tag>{{resume.proficiency}}</a-checkable-tag>
            <a-checkable-tag>{{resume.grade}}</a-checkable-tag>
          </a-col>
        </a-row>
      </a-card>
      <a-card title="项目经验">
        <a-row>
          <a-col :span="12" style="font-size: 1rem">
            <p>项目名称：{{resume.projectName}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>公司名称：{{resume.companyName}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>项目时间：{{resume.timeStart}}</p>
          </a-col>
          <a-col :span="12" style="font-size: 1rem">
            <p>项目时间：{{resume.timeEnd}}</p>
          </a-col>
        </a-row>
        <a-row>
          <a-col :span="3" style="font-size: 1rem">
            <p>项目描述：</p>
          </a-col>
          <a-col :span="21" style="font-size: 1rem">
            <a-textarea :autosize="{ minRows: 4, maxRows: 12 }" :placeholder="resume.description"/>
          </a-col>
        </a-row>
        <a-row style="margin-top:10px">
          <a-col :span="3" style="font-size: 1rem">
            <p>项目职责：</p>
          </a-col>
          <a-col :span="21" style="font-size: 1rem">
            <a-textarea :autosize="{ minRows: 4, maxRows: 12 }" :placeholder="resume.duty"/>
          </a-col>
        </a-row>
      </a-card>
      <a-card>
        <a-row style="margin-top:10px">
          <a-col :span="3" style="font-size: 1rem">
            <p>自我评价</p>
          </a-col>
          <a-col :span="21" style="font-size: 1rem">
            <a-textarea :autosize="{ minRows: 4, maxRows: 12 }" :placeholder="resume.performance"/>
          </a-col>
        </a-row>
      </a-card>
      <a-card>
        <a-row>
          <a-col :span="3" style="font-size: 1rem">
            <p>附加信息：</p>
          </a-col>
          <a-col :span="21" style="font-size: 1rem">
            <a-textarea
              :autosize="{ minRows: 2, maxRows: 12 }"
              :placeholder="resume.addInformation"
            />
          </a-col>
        </a-row>
      </a-card>
      <a-card title="简历附件">
        <a-row style="text-align: left;margin-top:10px">
          <a-col :span="4">
            <a-button @click="viewResume" v-if="resumename != ''">
              <a-icon type="file-pdf"/>
              {{resumeTitle}}
            </a-button>
          </a-col>
          <a-col :span="3">
            <a-button @click="downloadResume" v-if="resumename != ''">
              <a-icon type="download"/>下载
            </a-button>
          </a-col>
          <a-col :span="3">
            <a-upload
              :remove="handleRemove"
              :disabled="fileList.length === 1"
              :beforeUpload="beforeUpload"
              :fileList="fileList"
            >
              <a-button>选择简历</a-button>
            </a-upload>
          </a-col>
          <a-col :span="3">
            <a-button
              @click="handleUploadResume"
              :disabled="fileList.length === 0"
              :loading="uploading"
            >
              <a-icon type="upload"/>
              {{uploading ? '上传中' : '上传' }}
            </a-button>
          </a-col>
        </a-row>
      </a-card>
      <a-card>
        <pdf-info :pdfInfoVisiable="pdfInfo.visiable" :fileName="resumename"></pdf-info>
      </a-card>
    </a-card>
    <resume-edit
      ref="updateresume"
      @success="handleResumeEditSuccess"
      @close="handleResumeEditClose"
      :resumeEditVisiable="resumeEditVisiable"
    ></resume-edit>
  </div>
</template>
<script>
import { mapState, mapMutations } from "vuex";
import PdfInfo from "@/components/file/PdfInfo";
import ResumeEdit from "./ResumeEdit";
import moment from "moment";

function getBase64(img, callback) {
  const reader = new FileReader();
  reader.addEventListener("load", () => callback(reader.result));
  reader.readAsDataURL(img);
}

export default {
  name: "Resume",
  components: { ResumeEdit, PdfInfo },
  data() {
    return {
      pdfInfo: {
        visiable: false
      },
      loading: false,
      fileList: [],
      uploading: false,
      resumeEditVisiable: false,
      userId: "",
      username: "",
      resumeId: "",
      resume: {},
      resumeTitle: "预览简历"
    };
  },
  computed: {
    ...mapState({
      multipage: state => state.setting.multipage,
      user: state => state.account.user
    }),
    photo() {
      return `http://pqggd9642.bkt.clouddn.com/${this.resume.photo}`;
    },
    resumename() {
      return `http://pqggd9642.bkt.clouddn.com/${this.user.resume}`;
    },
    birthday() {
      return moment(this.user.birthday).format("YYYY-MM-DD");
    },
    sex() {
      switch (this.user.ssex) {
        case "0":
          return "男";
        case "1":
          return "女";
        case "2":
          return "保密";
        default:
          return this.user.ssex;
      }
    }
  },
  methods: {
    moment,
    ...mapMutations({
      setUser: "account/setUser"
    }),
    resumeEdit() {
      this.$refs.updateresume.setFormValues(this.resume);
      this.resumeEditVisiable = true;
    },
    viewResume() {
      if (this.pdfInfo.visiable == true) {
        (this.resumeTitle = "预览简历"), (this.pdfInfo.visiable = false);
      } else {
        (this.resumeTitle = "关闭简历"), (this.pdfInfo.visiable = true);
      }
    },
    downloadResume() {
      this.$download(`${this.resumename}`, {}, this.user.resume);
    },
    handleResumeEditClose() {
      this.resumeEditVisiable = false;
    },
    handleResumeEditSuccess() {
      this.resumeEditVisiable = false;
      this.$message.success("修改成功");
    },
    getResume() {
      this.resume.userId = this.user.userId;
      this.$get("resume", { ...this.resume })
        .then(r => {
          this.loading = false;
          this.resume = r.data.rows[0];
          console.log(this.resume);
        })
        .catch(e => {
          console.error(e);
          this.$message.error("获取简历信息");
        });
    },

    handleRemove(file) {
      if (this.uploading) {
        this.$message.warning("文件导入中，请勿删除");
        return;
      }
      const index = this.fileList.indexOf(file);
      const newFileList = this.fileList.slice();
      newFileList.splice(index, 1);
      this.fileList = newFileList;
    },
    beforeUpload(file) {
      this.fileList = [...this.fileList, file];
      return false;
    },
    handleUploadAvatar() {
      const { fileList } = this;
      const formData = new FormData();
      let flieArr = (fileList[0].name || "").split(".");
      let suffix = flieArr[flieArr.length - 1];

      if (suffix != "jpg" && suffix != "png") {
        this.$message.warning("请上传jpg或者png图片!");
        this.fileList = [];
      } else {
        formData.append("file", fileList[0]);
        formData.append("resumeId", this.resume.resumeId);
        formData.append("key", this.resume.photo);
        this.uploading = true;
        this.$upload("resume/uploadAvatar", formData)
          .then(r => {
            let data = r.data;
            this.$message.success("上传成功!");
            this.fileList = [];
            this.uploading = false;
            this.resume.photo = data.filename;
          })
          .catch(r => {
            console.error(r);
            this.uploading = false;
            this.fileList = [];
          });
      }
    },
    handleUploadResume() {
      const { fileList } = this;
      const formData = new FormData();
      let flieArr = (fileList[0].name || "").split(".");
      let suffix = flieArr[flieArr.length - 1];

      if (suffix != "docx" && suffix != "doc" && suffix != "pdf") {
        this.$message.warning("请上传pdf或者word文档!");
        this.fileList = [];
      } else {
        formData.append("file", fileList[0]);
        formData.append("username", this.user.username);
        formData.append("key", this.user.resume);
        this.uploading = true;
        this.$upload("user/uploadResume", formData)
          .then(r => {
            let data = r.data;
            this.$message.success("上传简历成功!");
            this.uploading = false;
            let user = this.user;
            user.resume = data.filename;
            this.setUser(user);
            this.fileList = [];
          })
          .catch(r => {
            console.error(r);
            this.uploading = false;
            this.fileList = [];
          });
      }
    }
  },
  mounted() {
    this.getResume();
  }
};
</script>
<style lang="less">
.resume-resume {
  .ant-card-body {
    padding: 1rem 0 !important;

    p {
      font-size: 0.9rem !important;
      margin-bottom: 0.6rem !important;
    }
  }
}
</style>
