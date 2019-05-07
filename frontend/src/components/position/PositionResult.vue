<template>
  <div class="div">
  <div class="left">
    <p style="margin-top: 10px">Java开发工程师</p>
    <span>
      <ul>
        <li>12-22万</li>
        <li>上海浦东新区</li>
        <li>大专及以上</li>
        <li>3年以上</li>
      </ul>
    </span>
    <span>
      <ul>
        <li>16小时前</li>
        <li>投递后：5个工作日反馈</li>
      </ul>
    </span>
  </div>
  <div class="right">
    <p style="margin-top: 10px">雪莲科技</p>
    <span>
      <ul>
        <li>互联网金融</li>
        <li>B轮</li>
      </ul>
    </span>
    <span>
      <ul>
        <li>五险一金</li>
        <li>午餐补助</li>
        <li>定期体检</li>
      </ul>
    </span>
  </div>
  </div>
</template>

<script>
export default {
  name: 'PositionResult'

}
</script>

<style lang="less" scoped>
  .div{
    display: block;
    margin-top: 50px;
  }
  .div ul{
    list-style:none; 
    margin: 0px;
    padding: 0;
    width: auto;
  }
  .div ul li{
    float:left;
    margin-right: 5px;
  }
  .left{
    text-decoration: none;
    float: left;
    width: 400px;
  }
  .right{
    text-decoration: none;
    float: left;
    width: 200px;
  }
  

</style>

