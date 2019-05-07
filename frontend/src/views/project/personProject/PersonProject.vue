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
              <a-form-item label="人才名称" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.personName"/>
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
        <a-button type="primary" ghost @click="addContact">添加联系</a-button>
        <a-button type="primary" ghost @click="comment">猎头评语</a-button>
        <collection-create-form
          ref="collectionForm"
          :visible="visible"
          @cancel="handleCancel"
          @create="handleCreate"
        />
        <a-button type="primary" ghost @click="newSearch">陌生寻访</a-button>
        <a-button type="primary" ghost @click="interviewLink">面试联系</a-button>
        <a-button type="primary" ghost @click="referenceCheck">背景调查</a-button>
        <a-dropdown>
          <a-menu slot="overlay">
            <a-menu-item key="export-data" @click="exportExcel">导出Excel</a-menu-item>
          </a-menu>
          <a-button>
            更多操作
            <a-icon type="down"/>
          </a-button>
        </a-dropdown>
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
            type="eye"
            theme="twoTone"
            twoToneColor="#42b983"
            @click="view(record)"
            title="详细信息"
          ></a-icon>
          <!-- <a-badge status="warning" text="无权限"></a-badge> -->
        </template>
      </a-table>
    </div>
  </a-card>
</template>

<script>
import RangeDate from "@/components/datetime/RangeDate";
import GatherStatusInputTree from "@/components/dropbox/GatherStatusInputTree";
import ProjectStatusInputTree from "@/components/dropbox/ProjectStatusInputTree";
import { mapState } from "vuex";

const CollectionCreateForm = {
  props: ["visible"],
  beforeCreate() {
    this.form = this.$form.createForm(this);
  },
  template: `
    <a-modal
      :visible="visible"
      title='人才评语'
      okText='提交'
      @cancel="() => { $emit('cancel') }"
      @ok="() => { $emit('create') }"
    >
      <a-form layout='vertical' :form="form">
        <a-form-item label='评级'>
          <a-input
            v-decorator="[
              'title',
              {
                rules: [{ required: true, message: 'Please input the title of collection!' }],
              }
            ]"
          />
        </a-form-item>
        <a-form-item label='备注'>
          <a-input
            type='textarea'
            v-decorator="['description']"
          />
        </a-form-item>
       
      </a-form>
    </a-modal>
  `
};

export default {
  name: "PersonProject",
  components: {
    RangeDate,
    GatherStatusInputTree,
    ProjectStatusInputTree,
    CollectionCreateForm
  },
  // ProjectInfo,ProjectEdit,ProjectAdd,
  data() {
    return {
      advanced: false,
      visible: false,
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
      queryParams: {
      },
      projectContact: {
        personProjectId: "",
        contactType: ""
      },
      personProject: {
        personProjectId: ""
      },
      filteredInfo: null,
      sortedInfo: null,
      paginationInfo: null,
      dataSource: [],
      selectedRowKeys: [],
      loading: false,
      userId: "",
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
          title: "人才姓名",
          dataIndex: "personName",
          sorter: true,
          sortOrder: sortedInfo.columnKey === "personName" && sortedInfo.order
        },
        {
          title: "人才状态",
          dataIndex: "personStatus"
        },
        {
          title: "联系方式",
          dataIndex: "phone"
        },
        {
          title: "所属项目",
          dataIndex: "projectName",
          sorter: true,
          sortOrder: sortedInfo.columnKey === "projectName" && sortedInfo.order
        },
        {
          title: "项目状态",
          dataIndex: "projectStatus",
          customRender: (text, row, index) => {
            switch (text) {
              case "0":
                return <a-tag color="red">进行中</a-tag>;
              case "1":
                return <a-tag color="cyan">完成</a-tag>;
              default:
                return text;
            }
          },
          filters: [
            { text: "完成", value: "1" },
            { text: "进行中", value: "0" }
          ],
          filterMultiple: false,
          filteredValue: filteredInfo.status || null,
          onFilter: (value, record) => record.status.includes(value)
        },
        {
          title: "项目收款状态",
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
          title: "客户名称",
          dataIndex: "companyName"
        },
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
      this.$message.warning("详情请到人才库查看！")
    },
    addContact() {
      if (
        this.selectedRowKeys.length === 0 ||
        this.selectedRowKeys.length > 1
      ) {
        this.$message.warning("您未选择数据或者选择的数据大于一条！");
        return false;
      } else {
        this.projectContact.personProjectId = this.dataSource[
          this.selectedRowKeys
        ].personProjectId;
        this.projectContact.contactType = "普通联系";
        this.projectContact.userId = this.user.userId;
        console.log(this.projectContact);
        if (!this.personProjectId) {
          this.$post("projectContact", {
            ...this.projectContact
          })
            .then(() => {
              this.$message.success("添加联系成功");
              this.selectedRowKeys = [];
            })
            .catch(() => {
              this.loading = false;
            });
        }
      }
    },
    newSearch() {
      if (
        this.selectedRowKeys.length === 0 ||
        this.selectedRowKeys.length > 1
      ) {
        this.$message.warning("您未选择数据或者选择的数据大于一条！");
        return false;
      } else {
        this.projectContact.personProjectId = this.dataSource[
          this.selectedRowKeys
        ].personProjectId;
        this.projectContact.contactType = "陌生寻访";
        this.projectContact.userId = this.user.userId;
        console.log(this.projectContact);
        if (!this.personProjectId) {
          this.$post("projectContact", {
            ...this.projectContact
          })
            .then(() => {
              this.$message.success("添加陌生寻访成功");
              this.selectedRowKeys = [];
            })
            .catch(() => {
              this.loading = false;
            });
        }
      }
    },
    interviewLink() {
      if (
        this.selectedRowKeys.length === 0 ||
        this.selectedRowKeys.length > 1
      ) {
        this.$message.warning("您未选择数据或者选择的数据大于一条！");
        return false;
      } else {
        this.projectContact.personProjectId = this.dataSource[
          this.selectedRowKeys
        ].personProjectId;
        this.projectContact.contactType = "面试联系";
        this.projectContact.userId = this.user.userId;
        console.log(this.projectContact);
        if (!this.personProjectId) {
          this.$post("projectContact", {
            ...this.projectContact
          })
            .then(() => {
              this.$message.success("添加面试联系成功");
              this.selectedRowKeys = [];
            })
            .catch(() => {
              this.loading = false;
            });
        }
      }
    },
    referenceCheck() {
      if (
        this.selectedRowKeys.length === 0 ||
        this.selectedRowKeys.length > 1
      ) {
        this.$message.warning("您未选择数据或者选择的数据大于一条！");
        return false;
      } else {
        this.projectContact.personProjectId = this.dataSource[
          this.selectedRowKeys
        ].personProjectId;
        this.projectContact.contactType = "背景调查";
        this.projectContact.userId = this.user.userId;
        console.log(this.projectContact);
        if (!this.personProjectId) {
          this.$post("projectContact", {
            ...this.projectContact
          })
            .then(() => {
              this.$message.success("添加背景调查成功");
              this.selectedRowKeys = [];
            })
            .catch(() => {
              this.loading = false;
            });
        }
      }
    },
    comment() {
      if (
        this.selectedRowKeys.length === 0 ||
        this.selectedRowKeys.length > 1
      ) {
        this.$message.warning("您未选择数据或者选择的数据大于一条！");
        return false;
      } else {
        this.visible = true;
      }
    },
    handleCancel() {
      this.visible = false;
    },
    handleCreate() {
      const form = this.$refs.collectionForm.form;
      form.validateFields((err, values) => {
          if (err) {
            return;
          }
          this.personProject.title = values.title;
          this.personProject.description = values.description;
          this.personProject.personProjectId = this.dataSource[this.selectedRowKeys].personProjectId;
          console.log(this.personProject);
          this.$put("personProject", {
            ...this.personProject
          })
            .then(r => {
              this.$message.success("评语提交成功!");
            })
            .catch(() => {
              this.$message.success("评语提交失败!");
            });
          form.resetFields();
          this.visible = false;
      });
    },
    handleGatherStatusChange(value) {
      this.queryParams.gatherStatus = value || "";
    },
    handleProjectStatusChange(value) {
      this.queryParams.projectStatus = value || "";
    },
    exportExcel() {
      let { sortedInfo, filteredInfo } = this;
      let sortField, sortOrder;
      // 获取当前列的排序和列的过滤规则
      if (sortedInfo) {
        sortField = sortedInfo.field;
        sortOrder = sortedInfo.order;
      }
      this.$export("personProject/excel", {
        sortField: sortField,
        sortOrder: sortOrder,
        ...this.queryParams,
        ...filteredInfo
      });
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
    fetch(params = { userId: this.user.userId }) {
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
      this.$get("personProject", {
        ...params
      }).then(r => {
        let data = r.data;
        const pagination = { ...this.pagination };
        pagination.total = data.total;
        this.dataSource = data.rows;
        // console.log(this.dataSource)
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
