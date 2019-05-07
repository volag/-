<template>
  <div class="position">
    <!-- 表格区域 -->
    <a-table
      ref="TableInfo"
      :columns="columns"
      :dataSource="dataSource"
      :pagination="pagination"
      :loading="loading"
      :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
      :scroll="{ x: 1350 }"
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
        <a-button type="danger" icon="minus" @click="cancelCollect(record)">取消收藏</a-button>
      </template>
    </a-table>
  </div>
</template>
<script>
import { mapState } from "vuex";
export default {
  name: "Collect",
  data() {
    return {
      userId: '',
      positionInfo: {
        data: {}
      },
      queryParams: {
      },
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
    cancelCollect(record) {
      this.positionInfo.data = record;
      let that = this;
      this.$confirm({
        title: "确定取消当前收藏的职位?",
        content: "当您点击确定按钮后，记录将会被彻底删除",
        centered: true,
        onOk() {
          let positionCollectIds = [];
          console.log(that.positionInfo.data.collectId);
          positionCollectIds.push(that.positionInfo.data.collectId);
          that.$delete("positionCollect/" + positionCollectIds.join(",")).then(() => {
            that.$message.success("删除成功");
            that.onSearch();
          });
        },
        onCancel() {
        }
      });
    },
    onSearch() {
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
      this.$get("position/collect", {
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
