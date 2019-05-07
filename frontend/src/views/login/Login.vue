<template>
  <div class="login">
    <a-form @submit.prevent="doLogin" :autoFormCreate="(form) => this.form = form">
      <a-tabs size="large" :tabBarStyle="{textAlign: 'center'}" style="padding: 0 2px;" :activeKey="activeKey"
              @change="handleTabsChange">
        <a-tab-pane tab="账户密码登录" key="1">
          <a-alert type="error" :closable="true" v-show="error" :message="error" showIcon
                   style="margin-bottom: 24px;"></a-alert>
          <a-form-item
            fieldDecoratorId="name"
            :fieldDecoratorOptions="{rules: [{ required: true, message: '请输入账户名', whitespace: true}]}">
            <a-input size="large">
              <a-icon slot="prefix" type="user"></a-icon>
            </a-input>
          </a-form-item>
          <a-form-item
            fieldDecoratorId="password"
            :fieldDecoratorOptions="{rules: [{ required: true, message: '请输入密码', whitespace: true}]}">
            <a-input size="large" type="password">
              <a-icon slot="prefix" type="lock"></a-icon>
            </a-input>
          </a-form-item>
        </a-tab-pane>
        <a-tab-pane tab="手机号登录" key="2">
          <a-form-item>
            <a-input v-model="user_phone">
              <a-icon slot="prefix" type="mobile"></a-icon>
            </a-input>
          </a-form-item>
          <a-form-item>
            <a-row :gutter="8" style="margin: 0 -4px">
              <a-col :span="16">
                <a-input size="large" v-model="user_verif">
                  <a-icon slot="prefix" type="mail"></a-icon>
                </a-input>
              </a-col>
              <a-col :span="8" style="padding-left: 4px">
                <a-button style="width: 100%" class="captcha-button" size="large" @click="send_code" @disabled="isSend">{{verif_code}}</a-button>
              </a-col>
            </a-row>
          </a-form-item>
        </a-tab-pane>
      </a-tabs>
      <a-form-item>
        <a-button :loading="loading" style="width: 100%; margin-top: 4px" size="large" htmlType="submit" type="primary">
          登录
        </a-button>
      </a-form-item>
      <div>
        <a style="float: right" @click="regist">注册账户</a>
      </div>
    </a-form>
  </div>
</template>

<script>
import {mapMutations} from 'vuex'

export default {
  name: 'Login',
  data () {
    return {
      loading: false,
      error: '',
      activeKey: '1',
      verif_code: '获取验证码',
      timer:null,
      user_verif: '',
      verif:false,
      code:'',
      isSend:true,
      label_verif:false,
      user_phone: '',
      phone: false,
      label_phone: false,
    }
  },
  computed: {
    systemName () {
      return this.$store.state.setting.systemName
    },
    copyright () {
      return this.$store.state.setting.copyright
    }
  },
  created () {
    this.$db.clear()
    this.$router.options.routes = []
  },
  methods: {
    doLogin () {
      if (this.activeKey === '1') {
        // 用户名密码登录
        this.form.validateFields(['name', 'password'], (errors, values) => {
          if (!errors) {
            this.loading = true
            let name = this.form.getFieldValue('name')
            let password = this.form.getFieldValue('password')
            this.$post('login', {
              username: name,
              password: password
            }).then((r) => {
              let data = r.data.data
              // console.log(data)
              this.saveLoginData(data)
              setTimeout(() => {
                this.loading = false
              }, 500)
              this.$router.push('/')
            }).catch(() => {
              setTimeout(() => {
                this.loading = false
              }, 500)
            })
          }
        })
      }
      if (this.activeKey === '2') {
        // 手机验证码登录
        this.form.validateFields(['user_phone', 'user_verif'], (errors, values) => {
          if (!errors) {
            this.loading = true
            console.log(this.user_phone,this.user_verif)
            this.$post('/message/login', {
              tel: this.user_phone,
              code: this.user_verif
            }).then((r) => {
              let data = r.data.data
              this.saveLoginData(data)
              setTimeout(() => {
                this.loading = false
              }, 500)
              this.$router.push('/')
            }).catch(() => {
              setTimeout(() => {
                this.loading = false
              }, 500)
            })
          }
        })
      }
    },
    regist () {
      this.$emit('regist', 'Regist')
    },
    handleTabsChange (val) {
      this.activeKey = val
    },
    ...mapMutations({
      setAddress: 'account/setAddress',
      setToken: 'account/setToken',
      setExpireTime: 'account/setExpireTime',
      setPermissions: 'account/setPermissions',
      setRoles: 'account/setRoles',
      setUser: 'account/setUser',
      setTheme: 'setting/setTheme',
      setLayout: 'setting/setLayout',
      setMultipage: 'setting/setMultipage',
      fixSiderbar: 'setting/fixSiderbar',
      fixHeader: 'setting/fixHeader',
      setColor: 'setting/setColor'
    }),
    saveLoginData (data) {
      this.setAddress(data.address)
      this.setToken(data.token)
      this.setExpireTime(data.exipreTime)
      this.setUser(data.user)
      this.setPermissions(data.permissions)
      this.setRoles(data.roles)
      this.setTheme(data.config.theme)
      this.setLayout(data.config.layout)
      this.setMultipage(data.config.multiPage === '1')
      this.fixSiderbar(data.config.fixSiderbar === '1')
      this.fixHeader(data.config.fixHeader === '1')
      this.setColor(data.config.color)
    },
    verif_show(){//不显示验证图标和规划
        this.verif=true;
        this.label_verif=true;
    },
    verif_display(){//恢复初始状态
        this.verif=false;
      if(this.user_verif==='')
      {
        this.label_verif=false
      }
      else{
        this.label_verif=true
      }
    },
    send_code(){//发送随机验证码
      if(this.user_phone==='')
      {
        this.$message.success('电话号码不能为空');
        return ;
      }
      else if(!(/^1[34578]\d{9}$/.test(this.user_phone)))
      {
        this.$message.success('电话号码格式不对');
        return ;
      }
      else {
        const count = 60;
        let times = 60;
        if (!this.timer)
        {
          this.isSend = false;//按钮禁用
          this.send_note(this.user_phone);//调用发送短信的方法
          this.timer = setInterval(()=> //倒计时;
            {
              if(times>0&&times<= count)
          {
            this.verif_code = times-- + 's后重新发送'
          }
        else
          {
            this.isSend = true;//按钮可用
            this.verif_code = '获取验证码';
            clearInterval(this.timer);
            this.timer = null;
          }
        },1000);
        }
      }
    },
    send_note(tel){//发送短信模板
      this.$post('message', {
              tel: tel
            }).then((r) => {
        this.loading = false
         let data = r.data.data
        console.log(data)
      }).catch((e) => {
        console.error(e)
        this.$message.error('获取验证码失败')
      })
    },
  
    // verif_Message() {//Vuex 将user_verif的状态保存到创库中
    //   store.commit('verif_Message',this.user_phone,this.code);
    //      }
    },
  }


</script>

<style lang="less" scoped>
  .login {
    .icon {
      font-size: 24px;
      color: rgba(0, 0, 0, 0.2);
      margin-left: 16px;
      vertical-align: middle;
      cursor: pointer;
      transition: color 0.3s;

      &:hover {
        color: #1890ff;
      }
    }
  }
</style>
