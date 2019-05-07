<template>
  <a-card :bordered="false" class="card-area">
    <div :class="advanced ? 'search' : null">
      <!-- 搜索区域 -->
      <a-form layout="horizontal">
        <a-row>
          <div :class="advanced ? null: 'fold'">
            <a-col :md="8" :sm="24">
              <a-form-item label="项目名称" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.projectName"/>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="项目编号" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.projectCode"/>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="项目状态" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <project-status-input-tree
                  @change="handleProjectStatusChange"
                  ref="projectStatusTree"
                ></project-status-input-tree>
              </a-form-item>
            </a-col>
            <template v-if="advanced">
              <a-col :md="8" :sm="24">
                <a-form-item label="客户名称" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                  <a-input v-model="queryParams.companyName"/>
                </a-form-item>
              </a-col>
            </template>
            <template v-if="advanced">
              <a-col :md="8" :sm="24">
                <a-form-item label="收款状态" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                  <gather-status-input-tree
                    @change="handleGatherStatusChange"
                    ref="gatherStatusTree"
                  ></gather-status-input-tree>
                </a-form-item>
              </a-col>
            </template>
            <template v-if="advanced">
              <a-col :md="8" :sm="24">
                <a-form-item label="创建时间" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                  <range-date @change="handleDateChange" ref="createTime"></range-date>
                </a-form-item>
              </a-col>
            </template>
          </div>
          <span style="float: right; margin-top: 3px;">
            <a-button type="primary" @click="search">查询</a-button>
            <a-button style="margin-left: 8px" @click="reset">重置</a-button>
            <a @click="toggleAdvanced" style="margin-left: 8px">
              {{advanced ? '收起' : '展开'}}
              <a-icon :type="advanced ? 'up' : 'down'"/>
            </a>
          </span>
        </a-row>
      </a-form>
    </div>
    <div>
      <div class="operator">
        <a-button type="primary" ghost @click="add" v-hasPermission="'project:add'">新增</a-button>
        <a-button type="primary" ghost @click="addProject">加入</a-button>
        <a-button @click="batchDelete" v-hasPermission="'project:delete'">删除</a-button>
        <a-dropdown>
          <a-menu slot="overlay">
            <a-menu-item
              key="export-data"
              @click="exportExcel"
              v-hasPermission="'project:export'"
            >导出Excel</a-menu-item>
          </a-menu>
          <a-button>
            更多操作
            <a-icon type="down"/>
          </a-button>
        </a-dropdown>
        <a-upload
          style=" display: inline;
      float: right;"
          :remove="handleRemove"
          :disabled="fileList.length === 1"
          :beforeUpload="beforeUpload"
          :fileList="fileList"
        >
          <a-button>
            <a-icon v-hasPermission="'project:import'" type="upload"/>项目信息导入
          </a-button>
        </a-upload>
        <a-button
          style=" display: inline;
      float: right;"
          @click="handleUpload"
          :disabled="fileList.length === 0"
          :loading="uploading"
        >{{uploading ? '导入中' : '导入数据' }}</a-button>
      </div>

      <!-- 表格区域 -->
      <a-table
        ref="TableInfo"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="pagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        :scroll="{ x: 900 }"
        @change="handleTableChange"
      >
        <template slot="email" slot-scope="text,record">
          <a-popover placement="topLeft">
            <template slot="content">
              <div>{{text}}</div>
            </template>
            <p style="width: 150px;margin-bottom: 0">{{text}}</p>
          </a-popover>
        </template>
        <template slot="operation" slot-scope="text, record">
          <a-icon
            v-hasPermission="'project:update'"
            type="setting"
            theme="twoTone"
            twoToneColor="#4a9ff5"
            @click="edit(record)"
            title="修改用户"
          ></a-icon>&nbsp;
          <a-icon
            v-hasPermission="'project:view'"
            type="eye"
            theme="twoTone"
            twoToneColor="#42b983"
            @click="view(record)"
            title="详细信息"
          ></a-icon>
          <a-badge v-hasNoPermission="'project:update','project:view'" status="warning" text="无权限"></a-badge>
        </template>
      </a-table>
    </div>
    <!-- 用户信息查看 -->
    <project-info
      :projectInfoData="projectInfo.data"
      :projectInfoVisiable="projectInfo.visiable"
      @close="handleprojectInfoClose"
    ></project-info>
    <!-- 新增用户 -->
    <project-add
      @close="handleprojectAddClose"
      @success="handleprojectAddSuccess"
      :projectAddVisiable="projectAdd.visiable"
    ></project-add>
    <!-- 修改用户 -->
    <project-edit
      ref="projectEdit"
      @close="handleprojectEditClose"
      @success="handleprojectEditSuccess"
      :projectEditVisiable="projectEdit.visiable"
    ></project-edit>
  </a-card>
</template>

<script>
import ProjectInfo from "./ProjectInfo";
import ProjectEdit from "./ProjectEdit";
import ProjectAdd from "./ProjectAdd";
import RangeDate from "@/components/datetime/RangeDate";
import GatherStatusInputTree from "@/components/dropbox/GatherStatusInputTree";
import ProjectStatusInputTree from "@/components/dropbox/ProjectStatusInputTree";
import { mapState } from "vuex";

export default {
  name: "Projects",
  // components: {projectInfo, projectAdd, projectEdit, DeptInputTree, RangeDate},
  components: {
    ProjectInfo,
    ProjectEdit,
    ProjectAdd,
    RangeDate,
    GatherStatusInputTree,
    ProjectStatusInputTree
  },
  data() {
    return {
      advanced: false,
      projectInfo: {
        visiable: false,
        data: {}
      },
      projectAdd: {
        visiable: false
      },
      projectEdit: {
        visiable: false
      },
      fileList: [],
      uploading: false,
      queryParams: {},
      filteredInfo: null,
      sortedInfo: null,
      paginationInfo: null,
      dataSource: [],
      selectedRowKeys: [],
      loading: false,
      personId: "",
      projectId: "",
      personProject: {},
      pagination: {
        pageSizeOptions: ["10", "20", "30", "40", "100"],
        defaultCurrent: 1,
        defaultPageSize: 10,
        showQuickJumper: true,
        showSizeChanger: true,
        showTotal: (total, range) =>
          `显示 ${range[0]} ~ ${range[1]} 条记录，共 ${total} 条记录`
      }
    };
  },
  computed: {
    ...mapState({
      user: state => state.account.user
    }),
    columns() {
      let { sortedInfo, filteredInfo } = this;
      sortedInfo = sortedInfo || {};
      filteredInfo = filteredInfo || {};
      return [
        {
          title: "项目名称",
          dataIndex: "projectName",
          sorter: true,
          sortOrder: sortedInfo.columnKey === "projectName" && sortedInfo.order
        },
        // {
        //   title: '邮箱',
        //   dataIndex: 'email',
        //   scopedSlots: { customRender: 'email' },
        //   width: 100
        // },
        {
          title: "开始日期",
          dataIndex: "startTime",
          sorter: true,
          sortOrder: sortedInfo.columnKey === "startTime" && sortedInfo.order
        },
        {
          title: "计划完成日期",
          dataIndex: "planFinishTime",
          sorter: true,
          sortOrder:
            sortedInfo.columnKey === "planFinishTime" && sortedInfo.order
        },
        {
          title: "项目状态",
          dataIndex: "projectStatus",
          customRender: (text, row, index) => {
            switch (text) {
              case "0":
                return <a-tag color="blue">进行中</a-tag>;
              case "1":
                return <a-tag color="cyan">完成</a-tag>;
              case "2":
                return <a-tag color="purple">暂停</a-tag>;
              case "3":
                return <a-tag color="red">终止</a-tag>;
              case "4":
                return <a-tag color="orange">未开始</a-tag>;

              default:
                return text;
            }
          },
          filters: [
            { text: "完成", value: "1" },
            { text: "进行中", value: "0" }
          ],
          filterMultiple: false,
          filteredValue: filteredInfo.projectStatus || null,
          onFilter: (value, record) => record.projectStatus.includes(value)
        },
        {
          title: "优先级别",
          dataIndex: "priorityLevel"
        },
        {
          title: "客户名称",
          dataIndex: "companyName"
        },
        {
          title: "收款状态",
          dataIndex: "gatherStatus",
          customRender: (text, row, index) => {
            switch (text) {
              case "0":
                return <a-tag color="red">未收款</a-tag>;
              case "1":
                return <a-tag color="cyan">已收款</a-tag>;
              default:
                return text;
            }
          },
          filters: [
            { text: "已收款", value: "1" },
            { text: "未收款", value: "0" }
          ],
          filterMultiple: false,
          filteredValue: filteredInfo.gatherStatus || null,
          onFilter: (value, record) => record.gatherStatus.includes(value)
        },
        {
          title: "客户经理",
          dataIndex: "customerManager"
        },
        {
          title: "业务人员",
          dataIndex: "businessPerson"
        },
        {
          title: "项目主管",
          dataIndex: "projectCharge"
        },
        // {
        //   title: '创建时间',
        //   dataIndex: 'createTime',
        //   sorter: true,
        //   sortOrder: sortedInfo.columnKey === 'createTime' && sortedInfo.order
        // },
        {
          title: "操作",
          dataIndex: "operation",
          scopedSlots: { customRender: "operation" }
        }
      ];
    }
  },
  mounted() {
    this.fetch();
    this.personId = this.$route.query.id;
  },
  methods: {
    onSelectChange(selectedRowKeys) {
      this.selectedRowKeys = selectedRowKeys;
    },
    toggleAdvanced() {
      this.advanced = !this.advanced;
      if (!this.advanced) {
        this.queryParams.createTimeFrom = "";
        this.queryParams.createTimeTo = "";
      }
    },
    view(record) {
      this.projectInfo.data = record;
      this.projectInfo.visiable = true;
    },
    add() {
      this.projectAdd.visiable = true;
    },
    addProject() {
      if (!this.personId || this.selectedRowKeys.length == 1) {
        this.personProject.personId = this.personId;
        this.personProject.projectId = this.dataSource[
          this.selectedRowKeys
        ].projectId;
        this.personProject.userId = this.user.userId;
        this.$post("personProject", {
          ...this.personProject
        })
          .then(() => {
            this.$route.query.arry = [];
            this.personId = this.$route.query.arry;
            this.$message.success("项目添加人才成功！");
          })
          .catch(() => {
            this.loading = false;
          });
      } else {
        this.$message.warning("选择的人才项目数据数量不是一对一！");
      }
    },
    handleprojectAddClose() {
      this.projectAdd.visiable = false;
    },
    handleprojectAddSuccess() {
      this.projectAdd.visiable = false;
      this.$message.success("新增人才信息成功");
      this.search();
    },
    edit(record) {
      this.$refs.projectEdit.setFormValues(record);
      this.projectEdit.visiable = true;
    },
    handleprojectEditClose() {
      this.projectEdit.visiable = false;
    },
    handleprojectEditSuccess() {
      this.projectEdit.visiable = false;
      this.$message.success("修改信息成功");
      this.search();
    },
    handleprojectInfoClose() {
      this.projectInfo.visiable = false;
    },
    handleGatherStatusChange(value) {
      console.log(value);
      this.queryParams.gatherStatus = value || "";
    },
    handleProjectStatusChange(value) {
      console.log(value);
      this.queryParams.projectStatus = value || "";
    },
    handleDateChange(value) {
      if (value) {
        this.queryParams.createTimeFrom = value[0];
        this.queryParams.createTimeTo = value[1];
      }
    },
    handleStartTimeChange(value) {
      if (value) {
        this.queryParams.startTimeFrom = value[0];
        this.queryParams.startTimeTo = value[1];
      }
    },
    handleplanFinishTimeChange(value) {
      if (value) {
        this.queryParams.planFinishTimeFrom = value[0];
        this.queryParams.planFinishTimeTo = value[1];
      }
    },
    batchDelete() {
      if (!this.selectedRowKeys.length) {
        this.$message.warning("请选择需要删除的记录");
        return;
      }
      let that = this;
      this.$confirm({
        title: "确定删除所选中的记录?",
        content: "当您点击确定按钮后，这些记录将会被彻底删除",
        centered: true,
        onOk() {
          let projectIds = [];
          for (let key of that.selectedRowKeys) {
            projectIds.push(that.dataSource[key].projectId);
          }
          that.$delete("project/" + projectIds.join(",")).then(() => {
            that.$message.success("删除成功");
            that.selectedRowKeys = [];
            that.search();
          });
        },
        onCancel() {
          that.selectedRowKeys = [];
        }
      });
    },
    exportExcel() {
      let { sortedInfo, filteredInfo } = this;
      let sortField, sortOrder;
      // 获取当前列的排序和列的过滤规则
      if (sortedInfo) {
        sortField = sortedInfo.field;
        sortOrder = sortedInfo.order;
      }
      this.$export("project/excel", {
        sortField: sortField,
        sortOrder: sortOrder,
        ...this.queryParams,
        ...filteredInfo
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
    handleUpload() {
      const { fileList } = this;
      const formData = new FormData();
      let flieArr = (fileList[0].name || "").split(".");
      let suffix = flieArr[flieArr.length - 1];
      if (suffix != "xlsx") {
        this.$message.warning("请上传Excel文件导入数据!");
        this.fileList = [];
      } else {
        formData.append("file", fileList[0]);
        this.uploading = true;
        this.$upload("project/import", formData)
          .then(r => {
            let data = r.data.data;
            this.$message.success("成功导入" + data.length + "条数据！");
            this.uploading = false;
            this.fileList = [];
            this.fetch();
          })
          .catch(r => {
            console.error(r);
            this.uploading = false;
            this.fileList = [];
          });
      }
    },
    search() {
      let { sortedInfo, filteredInfo } = this;
      let sortField, sortOrder;
      // 获取当前列的排序和列的过滤规则
      if (sortedInfo) {
        sortField = sortedInfo.field;
        sortOrder = sortedInfo.order;
      }
      this.fetch({
        sortField: sortField,
        sortOrder: sortOrder,
        ...this.queryParams,
        ...filteredInfo
      });
    },
    reset() {
      // 取消选中
      this.selectedRowKeys = [];
      // 重置分页
      this.$refs.TableInfo.pagination.current = this.pagination.defaultCurrent;
      if (this.paginationInfo) {
        this.paginationInfo.current = this.pagination.defaultCurrent;
        this.paginationInfo.pageSize = this.pagination.defaultPageSize;
      }
      // 重置列过滤器规则
      this.filteredInfo = null;
      // 重置列排序规则
      this.sortedInfo = null;
      // 重置查询参数
      this.queryParams = {};
      // 清空项目收款状态树选择
      this.$refs.gatherStatusTree.reset();
      // 清空项目状态树选择
      this.$refs.projectStatusTree.reset();
      // 清空时间选择
      if (this.advanced) {
        this.$refs.createTime.reset();
      }
      this.fetch();
    },
    handleTableChange(pagination, filters, sorter) {
      // 将这三个参数赋值给Vue data，用于后续使用
      this.paginationInfo = pagination;
      this.filteredInfo = filters;
      this.sortedInfo = sorter;

      this.projectInfo.visiable = false;
      this.fetch({
        sortField: sorter.field,
        sortOrder: sorter.order,
        ...this.queryParams,
        ...filters
      });
    },
    fetch(params = {}) {
      // 显示loading
      this.loading = true;
      if (this.paginationInfo) {
        // 如果分页信息不为空，则设置表格当前第几页，每页条数，并设置查询分页参数
        this.$refs.TableInfo.pagination.current = this.paginationInfo.current;
        this.$refs.TableInfo.pagination.pageSize = this.paginationInfo.pageSize;
        params.pageSize = this.paginationInfo.pageSize;
        params.pageNum = this.paginationInfo.current;
      } else {
        // 如果分页信息为空，则设置为默认值
        params.pageSize = this.pagination.defaultPageSize;
        params.pageNum = this.pagination.defaultCurrent;
      }
      this.$get("project", {
        ...params
      }).then(r => {
        let data = r.data;
        const pagination = { ...this.pagination };
        pagination.total = data.total;
        this.dataSource = data.rows;
        this.pagination = pagination;
        // 数据加载完毕，关闭loading
        this.loading = false;
      });
    }
  }
};
</script>
<style lang="less" scoped>
@import "../../../../static/less/Common";
</style>
