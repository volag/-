<template>
  <div id>
    <div class="top">
      <div class="top-left">
        <ul class="list-ul">
          <li>
            <h2>技术</h2>
            <a>PHP</a>
            <a>java</a>
            <a>数据挖掘</a>
            <a>IOS</a>
            <a>HTML5</a>
            <a>Web前端</a>
            <div class="xxnr">
              <div class="xxnr-detail">
                <h2>开发</h2>
                <a>Java</a>
                <a>PHP</a>
              </div>
              <div class="xxnr-detail">
                <h2>移动开发端</h2>
                <a>Java</a>
                <a>PHP</a>
              </div>
               <div class="xxnr-detail">
                <h2>测试</h2>
                <a>测试工程师</a>
                <a>自动化测试</a>
              </div>
            </div>
          </li>
          <li>
            <h2>产品</h2>
            <a>产品经理</a>
            <a>移动产品经理</a>
            <a>产品总监</a>
            <a>电商产品经理</a>
            <div class="xxnr">
              <div class="xxnr-detail">
                <h2>产品</h2>
                <a>产品经理</a>
                <a>网页产品经理</a>
              </div>
              <div class="xxnr-detail">
                <h2>产品设计师</h2>
                <a>网页产品设计师</a>
                <a>网页交互设计师</a>
              </div>
               <div class="xxnr-detail">
                <h2>用户研究</h2>
                <a>数据分析师</a>
                <a>用户研究员</a>
              </div>
            </div>
          </li>
          <li>
            <h2>设计</h2>
            <a>UI设计师</a>
            <a>APP设计师</a>
            <a>交互设计师</a>
            <a>设计总监</a>
            <div class="xxnr">
              <div class="xxnr-detail">
                <h2>视觉设计</h2>
                <a>UI设计</a>
                <a>视觉设计师</a>
              </div>
              <div class="xxnr-detail">
                <h2>交互设计</h2>
                <a>交互设计师</a>
                <a>网页交互设计师</a>
              </div>
               <div class="xxnr-detail">
                <h2>高端职位</h2>
                <a>设计经理</a>
                <a>设计总监</a>
              </div>
            </div>
          </li>
        </ul>
      </div>
      <div class="top-right">
        <div class="top-right-top">
          <a-form layout="horizontal">
            <a-input-search
              placeholder="输入职位关键名：如产品经理等"
              @search="onSearch"
              enterButton="搜索"
              size="large"
            />
          </a-form>
        </div>
        <div class="top-right-center">
          <span style="color:#999999">热门搜索：</span>
          <span>运营java</span>
          <span>产品经理</span>
          <span>销售</span>
          <span>ui设计师</span>
          <span>人力资源市场</span>
          <span>城市经理品牌</span>
        </div>
        <div class="top-right-bottom">
          <a-carousel autoplay>
            <div>
              <img src="https://image0.lietou-static.com/img/5c9db2ae509919fa0e9ab23901u.jpg">
            </div>
            <div>
              <img src="https://image0.lietou-static.com/img/5993e2e77032b02c641aa95004a.png">
            </div>
            <div>
              <img src="https://image0.lietou-static.com/img/5993e2e77032b02c641aa95004a.png">
            </div>
            <div>
              <img src="https://image0.lietou-static.com/img/5993e3d270320e1c3b4d500404a.png">
            </div>
          </a-carousel>
        </div>
      </div>
    </div>
    <div class="center">
      <div class="center-position">
        <div>
          <h2 style="margin-left:80px">热门职位</h2>
        </div>
        <div class="position" v-for="item in positionList" :key="item.id">
          <div class="left">
            <p style="margin-top: 10px">{{item.name}}</p>
            <span>
              <ul>
                <li>{{item.salary}}</li>
                <li>{{item.address}}</li>
                <li>{{item.needEducation}}</li>
                <router-link :to="{path: '/position/message',query:{id:item.id}}">
                  <li>{{item.name}}</li>
                </router-link>
              </ul>
            </span>
            <span>
              <ul>
                <li>{{item.needWorkTime}}</li>
                <li>{{item.issueTime}}</li>
              </ul>
            </span>
          </div>
          <div class="right">
            <router-link :to="{path: '/company/message',query:{id:item.issueCompany}}">
              <p style="margin-top: 10px">{{item.issueCompany}}</p>
            </router-link>
            <span>
              <ul>
                <li>{{item.description}}</li>
                <li>{{item.requested}}</li>
              </ul>
            </span>
            <span>
              <ul>
                <li>{{item.name}}</li>
                <li>{{item.name}}</li>
                <li>{{item.name}}</li>
              </ul>
            </span>
          </div>
        </div>
      </div>
      <div>
        <div style="width:960px">
          <h2 style="margin-left:80px">行业名企</h2>
        </div>
        <div class="company" v-for="item in companyList" :key="item.id">
          <div class="info">
            <span>
              <ul>
                <li>{{item.companyName}}</li>
                <li>{{item.tag1}}</li>
                <li>{{item.tag2}}</li>
              </ul>
            </span>
          </div>
        </div>
      </div>
    </div>
    <div class="bottom">
      <div></div>
      <div></div>
      <div></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HomePage",
  data() {
    return {
      positionList: [],
      companyList: [],
      position: {},
      company: {}
    };
  },
  methods: {
    onSearch(value) {
      console.log(value);
    },
    getPosition() {
      this.$get("position", this.position)
        .then(r => {
          this.loading = false;
          this.positionList = r.data.rows;
        })
        .catch(e => {
          console.error(e);
          this.$message.error("获取职位信息");
        });
    },
    getCompany() {
      this.$get("company", this.company)
        .then(r => {
          this.loading = false;
          this.companyList = r.data.rows;
        })
        .catch(e => {
          console.error(e);
          this.$message.error("获取公司信息");
        });
    }
  },
  mounted() {
    this.getPosition();
    this.getCompany();
  }
};
</script>

<style scoped>
.ant-carousel >>> .slick-slide {
  text-align: center;
  height: 230px;
  line-height: 160px;
  background: #364d79;
  overflow: hidden;
}

.ant-carousel >>> .slick-slide h3 {
  color: #fff;
}
.ant-row {
  left: 100px;
}
.top {
  margin-left: 100px;
  width: 1200px;
  height: 380px;
}
.top-left {
  width: 350px;
  height: 380px;
}
.top-right {
  width: 660px;
  height: 380px;
  float: left;
}
.top-right-center {
  margin-top: 20px;
}
.top-right-bottom {
  margin-top: 20px;
}
.center {
  width: 960px;
  margin-left: 67px;
}
.center-position{
  height: 1100px;
  width: 960px;
}
.position {
  display: block;
  margin-top: 20px;
  width: 700px;
  height: 50px;
  margin-left: 80px;
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
.left {
  text-decoration: none;
  float: left;
  width: 400px;
  height: 100px;
}
.right {
  text-decoration: none;
  float: left;
  width: 200px;
  height: 100px;
  margin-left: 100px;
}
.company {
  display: block;
  margin-top: 50px;
  width: 700px;
  margin-left: 80px;
}
.company .info {
  width: 200px;
  height: 250px;
  margin-right: 20px;
  margin-bottom: 20px;
  background-color: red;
  float: left;
}
.company ul {
  list-style: none;
  margin: 0px;
  padding: 0;
  width: auto;
}
.company ul li {
  float: left;
  margin-right: 5px;
}
.top-left .list-ul li {
  position: relative;
  box-sizing: border-box;
  padding: 8px 2px 0 8px;
  height: 100px;
  width: 300px;
  cursor: pointer;
  /* border-right: 1px solid transparent; */
  z-index: 4;
  list-style: none;
  background-color: #fafafa;
}
h2{
  color:#1889c2
}

.top-left .list-ul li span {
  float: left;
  margin-top: 4px;
  margin-left: 10px;
  font-size: 13px;
  color: black;
}

a {
  text-decoration: none;
  color: black;
}

.top-left .list-ul li a {
  float: left;
  margin-top: -3px;
  margin-right: 15px;
  font-size: 16px;
  color:#000
}

.top-left .list-ul .xxnr {
  position: absolute;
  top: -1px;
  left: 299px;
  width: 600px;
  height: 300px;
  text-align: left;
  display: none;
  background-color: #fff;
  /* z-index: 20; */
  border: 1px #1fa4f0 solid;
}
.xxnr-detail{
  width: 600px;
  height: 100px;
  margin-left:5px
}

.top-left .list-ul .xxnr span {
  margin: 30px;
}

.top-left .list-ul li:hover {
  border: 1px #1fa4f0 solid;
  border-right: 1px solid transparent;
}

.top-left .list-ul li:hover span {
  color: #000;
}

.top-left .list-ul li:hover a {
  color: #000;
}

.top-left .list-ul li:hover .xxnr {
  display: block;
}

.top-left,
.nav-c ul,
.nav-c ul li,
.carousel,
.carousel-r {
  float: left;
}
</style>



