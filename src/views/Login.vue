<template>
  <div class="login">
    <div class="weui-cells__title">登录</div>
    <div class="weui-cells weui-cells_form">
      <div class="weui-cell">
        <div class="weui-cell__hd">
          <label class="weui-label">账号</label>
        </div>
        <div class="weui-cell__bd">
          <input
            v-model="uname"
            class="weui-input"
            type="text"
            placeholder="请输入账号"
            autofocus
            v-on:keyup.enter="$refs.inputpwd.focus()"
          />
        </div>
      </div>
      <div class="weui-cell">
        <div class="weui-cell__hd">
          <label class="weui-label">密码</label>
        </div>
        <div class="weui-cell__bd">
          <input
            v-model="upwd"
            ref="inputpwd"
            class="weui-input"
            type="password"
            placeholder="请输入密码"
            v-on:keyup.enter="login"
          />
        </div>
      </div>
    </div>
    <div class="weui-cells__tips">联系行政获取初始账号密码</div>
    <div class="weui-btn-area">
      <a v-on:click="login" class="weui-btn weui-btn_block weui-btn_primary">登录</a>
    </div>
    <div class="page__ft j_bottom">
      <a href="https://weui.io">
        <img src="../assets/icon_footer.png" />
      </a>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import axios from 'axios'
import md5 from 'md5'
import weui from 'weui.js'
import * as define from '../define'

export default Vue.extend({
  name: 'login',
  data: () => {
    return {
      uname: '',
      upwd: ''
    }
  },
  methods: {
    // 需要标注有 `this` 参与运算的返回值类型
    login(): void {
      console.log(this.uname, this.upwd)
      const loading = weui.loading('正在登录中...')
      const url = `${define.API_HOST}/login`
      const account = this.uname
      const pwd = md5(this.upwd + account)
      const data = { account, pwd }
      axios
        .post(url, data, { timeout: 3000 })
        .then((response) => {
          loading.hide()
          const ret = response.data
          console.log('success', ret)
          localStorage.setItem('token', ret.token)
          if (ret.code === 0) {
            weui.toast('登录成功', {
              duration: 1000,
              callback: () => {
                this.$router.push('/')
              }
            })
          } else {
            const errorMsg = ret.msg
            weui.confirm(errorMsg)
          }
        })
        .catch((response) => {
          loading.hide()
          console.log('fail', response)
          weui.confirm('网络异常，请稍后重试')
        })
    }
  }
})
</script>
