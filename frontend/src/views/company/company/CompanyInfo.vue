<template>
  <a-modal
    v-model="show"
    :centered="true"
    :keyboard="false"
    :footer="null"
    :width="900"
    @cancel="handleCancleClick"
    title="公司信息"
  >
    <a-layout class="company-info">
      <a-layout-sider class="company-info-side">
        <a-avatar shape="square" :size="115" icon="user" :src="photo"/>
        <!-- <img style="width: 165px;height:160px;border-radius: 2px," alt="头像" :src="photo"> -->
        <a-upload
          :remove="handleRemove"
          :disabled="fileList.length === 1"
          :beforeUpload="beforeUpload"
          :fileList="fileList"
        >
          <a-button style="margin-left:10px;margin-top:5px">选择头像</a-button>
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
      </a-layout-sider>
      <a-layout-content class="company-content-one">
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type="user"/>
            公司名称:{{companyInfoData.companyName}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            公司地址:{{companyInfoData.companyAddress}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            融资情况:{{companyInfoData.finance}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            公司规模:{{companyInfoData.scale}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            公司性质:{{companyInfoData.nature}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            公司标签1:{{companyInfoData.tag1}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="8" :sm="48">
            <a-icon type/>
            公司标签2:{{companyInfoData.tag2}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            公司标签3:{{companyInfoData.tag3}}
          </a-col>
          <a-col :md="8" :sm="48">
            <a-icon type/>
            创建时间:{{companyInfoData.createTime}}
          </a-col>
        </a-row>
        <a-row style="margin-bottom:15px">
          <a-col :md="24" :sm="48">
            <a-icon type="company"/>
            公司介绍:{{companyInfoData.info}}
          </a-col>
        </a-row>
      </a-layout-content>
    </a-layout>
  </a-modal>
</template>
<script>
import moment from "moment";

function getBase64(img, callback) {
  const reader = new FileReader();
  reader.addEventListener("load", () => callback(reader.result));
  reader.readAsDataURL(img);
}

export default {
  name: "CompanyInfo",
  data() {
    return {
      fileList: [],
      uploading: false,
      loading: false
    };
  },
  props: {
    companyInfoVisiable: {
      require: true,
      default: false
    },
    companyInfoData: {
      require: true
    }
  },
  computed: {
    show: {
      get: function() {
        return this.companyInfoVisiable;
      },
      set: function() {}
    },
    photo(){
      return `http://pqggd9642.bkt.clouddn.com/${this.companyInfoData.avatar}`;
    }
  },
  methods: {
    handleCancleClick() {
      this.$emit("close");
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
        formData.append("companyId", this.companyInfoData.companyId);
        formData.append("key", this.companyInfoData.avatar);
        this.uploading = true;
        this.$upload("company/uploadAvatar", formData)
          .then(r => {
            let data = r.data;
            this.$message.success("上传成功!");
            this.fileList = [];
            this.uploading = false;
            this.companyInfoData.avatar = data.filename;
          })
          .catch(r => {
            console.error(r);
            this.uploading = false;
            this.fileList = [];
          });
      }
    }
  }
};
</script>
<style lang="less" scoped>
@import "CompanyInfo";
</style>
