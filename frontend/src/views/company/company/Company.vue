<template>
    <a-card :bordered="false" class="card-area">
    <div :class="advanced ? 'search' : null">
      <!-- 搜索区域 -->
      <a-form layout="horizontal">
        <a-row >
        <div :class="advanced ? null: 'fold'">
            <a-col :md="8" :sm="24" >
              <a-form-item
                label="名称"
                :labelCol="{span: 4}"
                :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.companyName"/>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24" >
              <a-form-item
                label="性质"
                :labelCol="{span: 4}"
                :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.nature"/>
              </a-form-item>
            </a-col>
            <a-col :md="8" :sm="24" >
              <a-form-item
                label="地址"
                :labelCol="{span: 4}"
                :wrapperCol="{span: 18, offset: 2}">
                <a-input v-model="queryParams.companyAddress"/>
              </a-form-item>
            </a-col>
          <template v-if="advanced">
            <a-col :md="8" :sm="24" >
              <a-form-item
                label="创建时间"
                :labelCol="{span: 4}"
                :wrapperCol="{span: 18, offset: 2}">
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
              <a-icon :type="advanced ? 'up' : 'down'" />
            </a>
          </span>
        </a-row>
      </a-form>
    </div>
    <div>
      <div class="operator">
        <a-button type="primary" ghost @click="add">新增</a-button>
        <a-button @click="batchDelete">删除</a-button>
        <a-dropdown>
          <a-menu slot="overlay">
            <a-menu-item key="export-data" @click="exportExcel">导出Excel</a-menu-item>
          </a-menu>
          <a-button>
            更多操作 <a-icon type="down" />
          </a-button>
        </a-dropdown>
      </div>
      <!-- 表格区域 -->
      <a-table ref="TableInfo"
               :columns="columns"
               :dataSource="dataSource"
               :pagination="pagination"
               :loading="loading"
               :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
               :scroll="{ x: 900 }"
               @change="handleTableChange">
        <template slot="email" slot-scope="text,record">
          <a-popover placement="topLeft">
            <template slot="content">
              <div>{{text}}</div>
            </template>
            <p style="width: 150px;margin-bottom: 0">{{text}}</p>
          </a-popover>
        </template>
        <template slot="operation" slot-scope="text, record">
          <a-icon type="setting" theme="twoTone" twoToneColor="#4a9ff5" @click="edit(record)" title="修改客户信息"></a-icon>
          &nbsp;
          <a-icon type="eye" theme="twoTone" twoToneColor="#42b983" @click="view(record)" title="详细信息"></a-icon>
          <!-- <a-badge status="warning" text="无权限"></a-badge> -->
        </template>
      </a-table>
    </div>
    <!-- 客户信息查看 -->
    <company-info
      :companyInfoData="companyInfo.data"
      :companyInfoVisiable="companyInfo.visiable"
      @close="handleCompanyInfoClose">
    </company-info>
    <!-- 新增客户 -->
    <company-add
      @close="handleCompanyAddClose"
      @success="handleCompanyAddSuccess"
      :companyAddVisiable="companyAdd.visiable">
    </company-add>
    <!-- 修改客户信息 -->
    <company-edit
      ref="companyEdit"
      @close="handleCompanyEditClose"
      @success="handleCompanyEditSuccess"
      :companyEditVisiable="companyEdit.visiable">
    </company-edit>
  </a-card>
</template>

<script>
import CompanyInfo from './CompanyInfo'
import CompanyEdit from './CompanyEdit'
import CompanyAdd from './CompanyAdd'
import RangeDate from '@/components/datetime/RangeDate'

export default {
    name: 'company',
    // components: {companyInfo, companyAdd, companyEdit, DeptInputTree, RangeDate},
    components: {CompanyInfo,CompanyEdit,CompanyAdd,RangeDate},
    data () {
    return {
      advanced: false,
      companyInfo: {
        visiable: false,
        data: {}
      },
      companyAdd: {
        visiable: false
      },
      companyEdit: {
        visiable: false
      },
      queryParams: {},
      filteredInfo: null,
      sortedInfo: null,
      paginationInfo: null,
      dataSource: [],
      selectedRowKeys: [],
      loading: false,
      pagination: {
        pageSizeOptions: ['10', '20', '30', '40', '100'],
        defaultCurrent: 1,
        defaultPageSize: 10,
        showQuickJumper: true,
        showSizeChanger: true,
        showTotal: (total, range) => `显示 ${range[0]} ~ ${range[1]} 条记录，共 ${total} 条记录`
      }
    }
  },
  computed: {
    columns () {
      let { sortedInfo, filteredInfo } = this
      sortedInfo = sortedInfo || {}
      filteredInfo = filteredInfo || {}
      return [{
        title: '公司名称',
        dataIndex: 'companyName',
        sorter: true,
        sortOrder: sortedInfo.columnKey === 'companyName' && sortedInfo.order
      }, 
      // {
      //   title: '邮箱',
      //   dataIndex: 'email',
      //   scopedSlots: { customRender: 'email' },
      //   width: 100
      // }, 
      {
        title: '公司地址',
        dataIndex: 'companyAddress',
      },
      {
        title: '融资情况',
        dataIndex: 'finance',
      },
      {
        title: '公司规模',
        dataIndex: 'scale'
      }, {
        title: '公司性质',
        dataIndex: 'nature'
      }, 
      {
        title: '公司行业',
        dataIndex: 'trade'
      }, 
      {
        title: '创建时间',
        dataIndex: 'createTime',
        sorter: true,
        sortOrder: sortedInfo.columnKey === 'createTime' && sortedInfo.order
      }, 
      {
        title: '操作',
        dataIndex: 'operation',
        scopedSlots: { customRender: 'operation' }
      }]
    }
  },
  mounted () {
    this.fetch()
  },
  methods: {
    onSelectChange (selectedRowKeys) {
      this.selectedRowKeys = selectedRowKeys
    },
    toggleAdvanced () {
      this.advanced = !this.advanced
      if (!this.advanced) {
        this.queryParams.createTimeFrom = ''
        this.queryParams.createTimeTo = ''
      }
    },
    view (record) {
      this.companyInfo.data = record
      this.companyInfo.visiable = true
    },
    add () {
      this.companyAdd.visiable = true
    },
    handleCompanyAddClose () {
      this.companyAdd.visiable = false
    },
    handleCompanyAddSuccess () {
      this.companyAdd.visiable = false
      this.$message.success('新增人才信息成功')
      this.search()
    },
    edit (record) {
      this.$refs.companyEdit.setFormValues(record)
      this.companyEdit.visiable = true
    },
    handleCompanyEditClose () {
      this.companyEdit.visiable = false
    },
    handleCompanyEditSuccess () {
      this.companyEdit.visiable = false
      this.$message.success('修改信息成功')
      this.search()
    },
    handleCompanyInfoClose () {
      this.companyInfo.visiable = false
    },
    // handleDeptChange (value) {
    //   this.queryParams.deptId = value || ''
    // },
    handleDateChange (value) {
      if (value) {
        this.queryParams.createTimeFrom = value[0]
        this.queryParams.createTimeTo = value[1]
      }
    },
    batchDelete () {
      if (!this.selectedRowKeys.length) {
        this.$message.warning('请选择需要删除的记录')
        return
      }
      let that = this
      this.$confirm({
        title: '确定删除所选中的记录?',
        content: '当您点击确定按钮后，这些记录将会被彻底删除',
        centered: true,
        onOk () {
          let companyIds = []
          for (let key of that.selectedRowKeys) {
            companyIds.push(that.dataSource[key].companyId)
          }
          that.$delete('company/' + companyIds.join(',')).then(() => {
            that.$message.success('删除成功')
            that.selectedRowKeys = []
            that.search()
          })
        },
        onCancel () {
          that.selectedRowKeys = []
        }
      })
    },
    exportExcel () {
      let {sortedInfo, filteredInfo} = this
      let sortField, sortOrder
      // 获取当前列的排序和列的过滤规则
      if (sortedInfo) {
        sortField = sortedInfo.field
        sortOrder = sortedInfo.order
      }
      this.$export('company/excel', {
        sortField: sortField,
        sortOrder: sortOrder,
        ...this.queryParams,
        ...filteredInfo
      })
    },
    search () {
      let {sortedInfo, filteredInfo} = this
      let sortField, sortOrder
      // 获取当前列的排序和列的过滤规则
      if (sortedInfo) {
        sortField = sortedInfo.field
        sortOrder = sortedInfo.order
      }
      this.fetch({
        sortField: sortField,
        sortOrder: sortOrder,
        ...this.queryParams,
        ...filteredInfo
      })
    },
    reset () {
      // 取消选中
      this.selectedRowKeys = []
      // 重置分页
      this.$refs.TableInfo.pagination.current = this.pagination.defaultCurrent
      if (this.paginationInfo) {
        this.paginationInfo.current = this.pagination.defaultCurrent
        this.paginationInfo.pageSize = this.pagination.defaultPageSize
      }
      // 重置列过滤器规则
      this.filteredInfo = null
      // 重置列排序规则
      this.sortedInfo = null
      // 重置查询参数
      this.queryParams = {}
      // 清空部门树选择
      // this.$refs.deptTree.reset()
      // 清空时间选择
      if (this.advanced) {
        this.$refs.createTime.reset()
      }
      this.fetch()
    },
    handleTableChange (pagination, filters, sorter) {
      // 将这三个参数赋值给Vue data，用于后续使用
      this.paginationInfo = pagination
      this.filteredInfo = filters
      this.sortedInfo = sorter

      this.companyInfo.visiable = false
      this.fetch({
        sortField: sorter.field,
        sortOrder: sorter.order,
        ...this.queryParams,
        ...filters
      })
    },
    fetch (params = {}) {
      // 显示loading
      this.loading = true
      if (this.paginationInfo) {
        // 如果分页信息不为空，则设置表格当前第几页，每页条数，并设置查询分页参数
        this.$refs.TableInfo.pagination.current = this.paginationInfo.current
        this.$refs.TableInfo.pagination.pageSize = this.paginationInfo.pageSize
        params.pageSize = this.paginationInfo.pageSize
        params.pageNum = this.paginationInfo.current
      } else {
        // 如果分页信息为空，则设置为默认值
        params.pageSize = this.pagination.defaultPageSize
        params.pageNum = this.pagination.defaultCurrent
      }
      this.$get('company', {
        ...params
      }).then((r) => {
        let data = r.data
        const pagination = { ...this.pagination }
        pagination.total = data.total
        this.dataSource = data.rows
        console.log(this.dataSource)
        this.pagination = pagination
        // 数据加载完毕，关闭loading
        this.loading = false
      })
    }
  }
}
</script>
<style lang="less" scoped>
@import "../../../../static/less/Common";
</style>
