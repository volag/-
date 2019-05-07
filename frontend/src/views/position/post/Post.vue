<template>
  <a-card :bordered="false" class="card-area">
    <div :class="advanced ? 'search' : null">
      <!-- 搜索区域 -->
      <a-form layout="horizontal">
        <a-row>
          <div :class="advanced ? null: 'fold'">
            <a-col :md="8" :sm="24">
              <a-form-item label="职位标题" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.postName"/>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="学历要求" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <education-background-input-tree
                  @change="onEducationBackgroundChange"
                  ref="educationBackgroundTree"
                ></education-background-input-tree>
              </a-form-item>
            </a-col>
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
        <a-button type="primary" ghost @click="add" v-hasPermission="'position:add'">新增</a-button>
        <a-button @click="batchDelete" v-hasPermission="'position:delete'">删除</a-button>
        <a-dropdown>
          <a-menu slot="overlay">
            <a-menu-item v-hasPermission="'position:export'" key="export-data" @click="exportExcel">导出Excel</a-menu-item>
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
            v-hasPermission="'position:update'"
            type="setting"
            theme="twoTone"
            twoToneColor="#4a9ff5"
            @click="edit(record)"
            title="修改职位"
          ></a-icon>
        </template>
        <a-badge v-hasNoPermission="'position:update','position:view'" status="warning" text="无权限"></a-badge>
      </a-table>
    </div>
    <!-- 新增项目 -->
    <post-add
      @close="handlePostAddClose"
      @success="handlePostAddSuccess"
      :postAddVisiable="postAdd.visiable"
    ></post-add>
    <!-- 修改项目 -->
    <post-edit
      ref="postEdit"
      @close="handlePostEditClose"
      @success="handlePostEditSuccess"
      :postEditVisiable="postEdit.visiable"
    ></post-edit>
  </a-card>
</template>
<script>
import { mapState } from "vuex";
import PostEdit from "./PostEdit";
import PostAdd from "./PostAdd";
import EducationBackgroundInputTree from "@/components/dropbox/EducationBackgroundInputTree";
import WorkYearsInputTree from "@/components/dropbox/WorkYearsInputTree";
import PositionSortInputTree from "@/components/dropbox/PositionSortInputTree";
import SalaryInputTree from "@/components/dropbox/SalaryInputTree";

export default {
  name: "Post",
  components: {
    PostEdit,
    PostAdd,
    EducationBackgroundInputTree,
    WorkYearsInputTree,
    PositionSortInputTree,
    SalaryInputTree
  },
  data() {
    return {
      userId: "",
      advanced: false,
      postAdd: {
        visiable: false
      },
      postEdit: {
        visiable: false
      },
      needEducation: "",
      queryParams: {},
      filteredInfo: null,
      sortedInfo: null,
      paginationInfo: null,
      dataSource: [],
      selectedRowKeys: [],
      loading: false,
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
          title: "职位标题",
          dataIndex: "positionName",
          sorter: true,
          sortOrder: sortedInfo.columnKey === "positionName" && sortedInfo.order
        },
        {
          title: "招聘地址",
          dataIndex: "address"
        },
        {
          title: "学历要求",
          dataIndex: "needEducation"
        },
        {
          title: "职位类型",
          dataIndex: "positionSort"
        },
        {
          title: "薪资",
          dataIndex: "salary"
        },
        {
          title: "发布的公司",
          dataIndex: "issueCompany"
        },
        {
          title: "工作经验",
          dataIndex: "needWorkTime"
        },
        {
          title: "发布时间",
          dataIndex: "issueTime"
        },
        {
          title: "操作",
          dataIndex: "operation",
          scopedSlots: { customRender: "operation" }
        }
      ];
    }
  },
  methods: {
    onSelectChange(selectedRowKeys) {
      this.selectedRowKeys = selectedRowKeys;
    },
    onEducationBackgroundChange(value) {
      this.needEducation = value || "";
      this.queryParams.needEducation = this.needEducation;
    },
    toggleAdvanced() {
      this.advanced = !this.advanced;
      if (!this.advanced) {
        this.queryParams.createTimeFrom = "";
        this.queryParams.createTimeTo = "";
      }
    },
    add() {
      this.postAdd.visiable = true;
    },
    handlePostAddClose() {
      this.postAdd.visiable = false;
    },
    handlePostAddSuccess() {
      this.postAdd.visiable = false;
      this.$message.success("职位发布成功！");
      this.search();
    },
    edit(record) {
      this.$refs.postEdit.setFormValues(record);
      this.postEdit.visiable = true;
    },
    handlePostEditClose() {
      this.postEdit.visiable = false;
    },
    handlePostEditSuccess() {
      this.postEdit.visiable = false;
      this.$message.success("修改信息成功");
      this.search();
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
          let positionIds = [];
          for (let key of that.selectedRowKeys) {
            console.log(that.dataSource[key].id);
            positionIds.push(that.dataSource[key].id);
          }
          that.$delete("position/" + positionIds.join(",")).then(() => {
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

      this.personInfo.visiable = false;
      this.queryParams.userId = this.user.userId;
      this.fetch({
        sortField: sorter.field,
        sortOrder: sorter.order,
        ...this.queryParams,
        ...filters
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
      this.$export("post/excel", {
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
      this.queryParams.userId = this.user.userId;
      this.fetch({
        sortField: sortField,
        sortOrder: sortOrder,
        ...this.queryParams,
        ...filteredInfo
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
      this.$get("position", {
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
  },
  mounted() {
    this.fetch();
  }
};
</script>
<style lang="less" scoped>
</style>
