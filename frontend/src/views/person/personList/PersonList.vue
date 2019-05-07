<template>
  <a-card :bordered="false" class="card-area">
    <div :class="advanced ? 'search' : null">
      <!-- 搜索区域 -->
      <a-form layout="horizontal">
        <a-row>
          <div :class="advanced ? null: 'fold'">
            <a-col :md="8" :sm="24">
              <a-form-item label="姓名" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.personName"/>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="人才类别" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <person-sort-input-tree @change="handlePersonSortChange" ref="personSortTree"></person-sort-input-tree>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24">
              <a-form-item label="人才级别" :labelCol="{span: 4}" :wrapperCol="{span: 18, offset: 2}">
                <person-level-input-tree @change="handlePersonLevelChange" ref="personLevelTree"></person-level-input-tree>
              </a-form-item>
            </a-col>
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
        <a-button type="primary" ghost @click="add"  v-hasPermission="'person:add'">新增</a-button>
        <router-link :to="{path: '/project/projects',query:{id:this.personId}}">
          <a-button type="primary" ghost @click="addProject">加入项目</a-button>
        </router-link>
        <a-button @click="batchDelete"  v-hasPermission="'person:delete'">删除</a-button>
        <a-dropdown>
          <a-menu slot="overlay">
            <a-menu-item key="export-data" @click="exportExcel"  v-hasPermission="'person:export'">导出Excel</a-menu-item>
          </a-menu>
          <a-button>
            更多操作
            <a-icon type="down"/>
          </a-button>
        </a-dropdown>
        <a-upload style=" display: inline;
      float: right;"
          :remove="handleRemove"
          :disabled="fileList.length === 1"
          :beforeUpload="beforeUpload"
          :fileList="fileList"
        >
          <a-button>
            <a-icon  v-hasPermission="'person:import'" type="upload"/>人才导入
          </a-button>
        </a-upload>
        <a-button style=" display: inline;
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
           v-hasPermission="'person:update'"
            type="setting"
            theme="twoTone"
            twoToneColor="#4a9ff5"
            @click="edit(record)"
            title="修改用户"
          ></a-icon>&nbsp;
          <a-icon
          v-hasPermission="'person:view'"
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
    <!-- 用户信息查看 -->
    <person-info
      :personInfoData="personInfo.data"
      :personInfoVisiable="personInfo.visiable"
      @close="handlepersonInfoClose"
    ></person-info>
    <!-- 新增用户 -->
    <person-add
      @close="handlepersonAddClose"
      @success="handlepersonAddSuccess"
      :personAddVisiable="personAdd.visiable"
    ></person-add>
    <!-- 修改用户 -->
    <person-edit
      ref="personEdit"
      @close="handlepersonEditClose"
      @success="handlepersonEditSuccess"
      :personEditVisiable="personEdit.visiable"
    ></person-edit>
  </a-card>
</template>

<script>
import PersonInfo from "./PersonInfo";
import PersonEdit from "./PersonEdit";
import PersonAdd from "./PersonAdd";
import PersonLevelInputTree from "@/components/dropbox/PersonLevelInputTree";
import PersonSortInputTree from "@/components/dropbox/PersonSortInputTree";
import RangeDate from "@/components/datetime/RangeDate";
import reqwest from "reqwest";

export default {
  name: "PersonList",
  // components: {personInfo, personAdd, personEdit, DeptInputTree, RangeDate},
  components: {
    PersonInfo,
    PersonEdit,
    PersonAdd,
    PersonLevelInputTree,
    PersonSortInputTree,
    RangeDate
  },
  data() {
    return {
      advanced: false,
      personInfo: {
        visiable: false,
        data: {}
      },
      personAdd: {
        visiable: false
      },
      personEdit: {
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
      personId: "",
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
    columns() {
      let { sortedInfo, filteredInfo } = this;
      sortedInfo = sortedInfo || {};
      filteredInfo = filteredInfo || {};
      return [
        {
          title: "姓名",
          dataIndex: "personName",
          sorter: true,
          sortOrder: sortedInfo.columnKey === "personName" && sortedInfo.order
        },
        {
          title: "邮箱",
          dataIndex: "email",
          scopedSlots: { customRender: "email" },
          width: 100
        },
        {
          title: "电话",
          dataIndex: "mobilePhone"
        },
        {
          title: "毕业院校",
          dataIndex: "graduateInstitutions"
        },
        {
          title: "专业",
          dataIndex: "major"
        },
        {
          title: "学历",
          dataIndex: "educationBackground"
        },
        {
          title: "人才类别",
          dataIndex: "personSort"
        },
        {
          title: "登记人",
          dataIndex: "userId"
        },
        {
          title: "人才级别",
          dataIndex: "personLevel"
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
      this.personInfo.data = record;
      this.personInfo.visiable = true;
    },
    add() {
      this.personAdd.visiable = true;
    },
    addProject() {
      if (
        this.selectedRowKeys.length === 0 ||
        this.selectedRowKeys.length > 1
      ) {
        this.$message.warning("您未选择数据或者选择的数据大于一条！");
        return false;
      } else {
        this.personId = this.dataSource[this.selectedRowKeys].personId;
        console.log(this.personId);
      }
    },
    handlepersonAddClose() {
      this.personAdd.visiable = false;
    },
    handlepersonAddSuccess() {
      this.personAdd.visiable = false;
      this.$message.success("新增人才信息成功");
      this.search();
    },
    edit(record) {
      this.$refs.personEdit.setFormValues(record);
      this.personEdit.visiable = true;
    },
    handlepersonEditClose() {
      this.personEdit.visiable = false;
    },
    handlepersonEditSuccess() {
      this.personEdit.visiable = false;
      this.$message.success("修改信息成功");
      this.search();
    },
    handlepersonInfoClose() {
      this.personInfo.visiable = false;
    },
    handlePersonSortChange(value) {
      this.queryParams.personSort = value || "";
    },
    handlePersonLevelChange(value) {
      this.queryParams.personLevel = value || "";
    },
    handleDateChange(value) {
      if (value) {
        this.queryParams.createTimeFrom = value[0];
        this.queryParams.createTimeTo = value[1];
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
          let personIds = [];
          for (let key of that.selectedRowKeys) {
            personIds.push(that.dataSource[key].personId);
          }
          that.$delete("person/" + personIds.join(",")).then(() => {
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
      this.$export("person/excel", {
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
      let flieArr = (fileList[0].name || '').split('.');
      let suffix = flieArr[flieArr.length - 1];
      if(suffix != 'xlsx'){
        this.$message.warning("请上传Excel文件导入数据!")
        this.fileList = [];
      }else{
        formData.append("file", fileList[0]);
      this.uploading = true;
      this.$upload("person/import", formData)
        .then(r => {
          let data = r.data.data
          this.$message.success('成功导入'+data.length+'条数据！')
          this.uploading = false;
          this.fileList = [];
          this.fetch()
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
      // 清空部门树选择
      // this.$refs.deptTree.reset()
      this.$refs.personLevelTree.reset();
      this.$refs.personSortTree.reset();
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
      this.$get("person", {
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
