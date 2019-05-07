<template>
  <div style="margin-left:50px;width:100%">
    <!-- 搜索区域 -->
    <a-form layout="horizontal">
      <a-input-search
      v-model="name"
        placeholder="输入职位关键词"
        @search="onSearch"
        enterButton="搜索"
        size="large"
      />
    </a-form>
    <a-row>
      <a-col :md="24" :sm="48" class="hotSearch">
        <span style="color:#999999">热门搜索：</span>
        <a-radio-group @change="onSearchChange">
          <a-radio-button
            v-for="(item,index) in hotSearchList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-radio-button>
        </a-radio-group>
      </a-col>
    </a-row>
    <a-row>
      <a-col :md="24" :sm="48" class="nomalSearch" style>
        <span style="color:#999999">公司：</span>
        <a-radio-group @change="onCompanyChange">
          <a-radio-button
            v-for="(item,index) in companyList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-radio-button>
        </a-radio-group>
      </a-col>
    </a-row>
    <a-row>
      <a-col :md="24" :sm="48" class="nomalSearch">
        <span style="color:#999999">行业：</span>
        <a-radio-group @change="onTradeChange">
          <a-radio-button
            v-for="(item,index) in tradeList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-radio-button>
        </a-radio-group>
      </a-col>
    </a-row>
    <a-row>
      <a-col :md="24" :sm="48" class="nomalSearch">
        <span style="color:#999999">城市：</span>
        <a-radio-group @change="onCityChange">
          <a-radio-button
            v-for="(item,index) in cityList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-radio-button>
        </a-radio-group>
      </a-col>
    </a-row>
    <a-row>
      <a-col :md="24" :sm="48" class="nomalSearch">
        <span style="color:#999999">薪资：</span>
        <a-radio-group @change="onSalaryChange">
          <a-radio-button
            v-for="(item,index) in salaryList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-radio-button>
        </a-radio-group>
      </a-col>
    </a-row>
    <a-row>
      <a-col :md="24" :sm="48" class="moreSearch">
        <span style="color:#999999">更多：</span>

        <a-select
          labelInValue
          :defaultValue="{ key: '发布时间' }"
          style="width: 120px"
          @change="onTimeChange"
        >
          <a-select-option
            v-for="(item,index) in timeList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-select-option>
        </a-select>
        <a-select
          labelInValue
          :defaultValue="{ key: '职位类型' }"
          style="width: 120px"
          @change="onPositionSortChange"
        >
          <a-select-option
            v-for="(item,index) in positionSortList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-select-option>
        </a-select>
        <a-select
          labelInValue
          :defaultValue="{ key: '企业规模' }"
          style="width: 120px"
          @change="onScaleChange"
        >
          <a-select-option
            v-for="(item,index) in scaleList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-select-option>
        </a-select>
        <a-select
          labelInValue
          :defaultValue="{ key: '企业性质' }"
          style="width: 120px"
          @change="onNatureChange"
        >
          <a-select-option
            v-for="(item,index) in natureList"
            :key="index"
            :value="item.value"
          >{{item.value}}</a-select-option>
        </a-select>
      </a-col>
    </a-row>
    <a-row>
      <template v-for="(tag, index) in tags">
        <a-tag :key="tag" :closable="index !== 0" :afterClose="() => handleClose(tag)">{{tag}}</a-tag>
      </template>
    </a-row>
    <div class="position">
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
            type="mail"
            theme="twoTone"
            twoToneColor="#4a9ff5"
            @click="send(record)"
            title="投递"
          ></a-icon>&nbsp;
          <a-icon
            type="plus-circle"
            theme="twoTone"
            twoToneColor="#42b983"
            @click="collect(record)"
            title="收藏"
          ></a-icon>
          <!-- <a-badge status="warning" text="无权限"></a-badge> -->
        </template>
      </a-table>
     
    </div>
  </div>
</template>
<script>
import { mapState } from "vuex";
// import store from '../store';

export default {
  name: "Search",
  data() {
    return {
      city: this.address,
      name: '',
      needEducation: '',
      positionSort: '',
      salary: '',
      issueCompany: '',
      issueTime: '',
      needWorkTime: '',
      inputValue: '',
      trade: '',
      scale: '',
      nature: '',
      position: {},
      userId: '',
      tags: ["搜索条件："],
      hotSearchList: [
        { value: "运营" },
        { value: "产品经理" },
        { value: "java" },
        { value: "销售" },
        { value: "ui设计师" },
        { value: "人力资源" },
        { value: "市场" },
        { value: "城市经理" },
        { value: "品牌" }
      ],
      companyList: [
        { value: "开发部" },
        { value: "腾讯" },
        { value: "阿里巴巴" },
        { value: "yoho" },
        { value: "滴滴" },
        { value: "盛大" }
      ],
      tradeList: [
        { value: "互联网" },
        { value: "网络游戏" },
        { value: "IT服务" },
        { value: "计算机硬件" },
        { value: "建筑工程" },
        { value: "银行" },
        { value: "机械制造" }
      ],
      cityList: [
        { value: "全国" },
        { value: "北京" },
        { value: "上海" },
        { value: "广州" },
        { value: "深圳" },
        { value: "天津" },
        { value: "苏州" },
        { value: "重庆" },
        { value: "南昌" },
        { value: "其他" }
      ],
      salaryList: [
        { value: "10-15万" },
        { value: "15-20万" },
        { value: "20-30万" },
        { value: "30-50万" },
        { value: "50-100万" },
        { value: "100万以上" },
      ],
      timeList: [
        { value: "一天以内" },
        { value: "三天以内" },
        { value: "一周以内" },
        { value: "一个月以内" }
      ],
      positionSortList: [
        { value: "不限" },
        { value: "猎头职位" },
        { value: "企业职位" }
      ],
      scaleList: [
        { value: "1-49" },
        { value: "50-99" },
        { value: "100-499" },
        { value: "500-999" },
        { value: "1000-2000" },
        { value: "2000以上" },
      ],
      natureList: [
        { value: "外企" },
        { value: "中外合营" },
        { value: "私营" },
        { value: "国有企业"},
        { value: "上市公司" },
        { value: "政府" },
        { value: "事业单位" },
        { value: "其他" }
      ],
      positionList: [],
      positionInfo: {
        data: {}
      },
      positionCollect: {},
      positionSend: {},
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
  //    filters: {
  //     //timeago.js插件
  //     //计算时间，类似于几分钟前，几小时前，几天前等
  //     changeTime(issueTime){
  //         let time = new Date("2019-03-29 14:38:11"); //先将接收到的json格式的日期数据转换成可用的js对象日期
  //         console.log(new timeago().format(time, 'zh_CN'));
  //         return new timeago().format(time, 'zh_CN'); //转换成类似于几天前的格式
  //     }
  // },
  computed: {
    ...mapState({
      user: state => state.account.user,
      address : state => state.account.address
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
    onSearchChange(e) {
      this.name = e.target.value;
    },
    onTradeChange(e) {
      this.trade = e.target.value;
    },
    onCompanyChange(e) {
      this.issueCompany = e.target.value;
    },
    onCityChange(e) {
      this.city = e.target.value;
    },
    onSalaryChange(e) {
      this.salary = e.target.value;
    },
    onTimeChange(e) { 
      this.issueTime =  e.key;
    },
    onPositionSortChange(e) {
      this.positionSort =  e.key;
    },
    onScaleChange(e) {
      this.scale = e.key;
    },
    onNatureChange(e) {
      this.nature =  e.key;
    },
    handleClose(removedTag) {
      const tags = this.tags.filter(tag => tag !== removedTag);
      console.log(tags);
      this.tags = tags;
    },
    onClickTime({ key }) {
      console.log(`Click on item ${key}`);
      this.tags.push(`${key}`)
    },
    onSelectChange(selectedRowKeys) {
      this.selectedRowKeys = selectedRowKeys;
    },
    send(record) {
      this.positionInfo.data = record;
      this.positionSend.userId = this.user.userId;
      this.positionSend.positionId = this.positionInfo.data.id;
      this.$get("positionSend", { ...this.positionSend }).then(r => {
        if (r.data) {
          console.log(r.data);
          this.$post("positionSend", {
            ...this.positionSend
          })
            .then(() => {
              this.$message.success("职位投递成功！");
              this.loading = false;
            })
            .catch(() => {
              this.loading = false;
            });
        } else {
          this.$message.warning("该职位已投递,请投递其他职位");
        }
      });
    },
    collect(record) {
      this.positionInfo.data = record;
      this.positionCollect.userId = this.user.userId;
      this.positionCollect.positionId = this.positionInfo.data.id;
      console.log(this.positionCollect);
      this.$get("positionCollect", { ...this.positionCollect }).then(r => {
        if (r.data) {
          this.$post("positionCollect", {
            ...this.positionCollect
          })
            .then(() => {
              this.$message.success("职位收藏成功！");
              this.loading = false;
            })
            .catch(() => {
              this.loading = false;
            });
        } else {
          this.$message.warning("该职位已收藏,请选择其他职位！");
        }
      });
    },
    onSearch(value) {
      this.queryParams.address = this.city;
      // this.queryParams.name = this.name;
      this.queryParams.needEducation = this.needEducation;
      this.queryParams.positionSort = this.positionSort;
      this.queryParams.salary = this.salary;
      this.queryParams.issueCompany = this.issueCompany;
      // this.queryParams.issueTime = this.issueTime;
      this.queryParams.needWorkTime = this.needWorkTime;
      this.queryParams.trade = this.trade;
      this.queryParams.scale = this.scale;
      this.queryParams.nature = this.nature; 
      this.queryParams.positionName = value;
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
      this.$post("position/condition", {
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
<style>
.hotSearch {
  float: left;
  margin-top: 10px;
  font-size: 15px;
  font-family: Microsoft Yahei;
}
.hotSearch .span {
  margin-right: 15px;
}
.nomalSearch {
  float: left;
  margin-top: 30px;
  font-size: 14px;
  font-family: Microsoft Yahei;
}
.nomalSearch .span {
  margin-right: 15px;
}
.moreSearch {
  float: left;
  margin-top: 30px;
  margin-bottom: 30px;
  padding-right: 10px;
  font-size: 15px;
  font-family: Microsoft Yahei;
}
.moreSearch .span {
  margin-right: 15px;
}
.position {
  display: block;
  margin-top: 50px;
}
.position ul {
  list-style: none;
  margin: 0px;
  padding: 0;
  width: auto;
}
.position ul li {
  float: left;
  margin-right: 5px;
}
</style>
