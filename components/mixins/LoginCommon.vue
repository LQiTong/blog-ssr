<template>
  <div class="login-page">
    正在进行第三方登录，请稍候
  </div>
</template>

<script>
import { setToken } from '@/tool'

export default {
  layout: '',
  created () {
    if (process.client) {
      let promise = this.loginWithCode()
      promise && promise.then(this.loginCallback).catch(this.loginFail)
    }
  },
  methods: {
    login (state) {
      state ? this.$Message.success('登录成功') : this.$Message.error('登录失败，请重试')
      if (this.$isPC) {
        const data = { state, type: 'login-state' }
        console.log(window.opener)
        window.opener && window.opener.postMessage(data)
        window.close()
      } else {
        this.$store.dispatch('userInfo/getUserInfo', { force: true })
        let redirect = localStorage.getItem('loginRedirect') || '/'
        this.$router.replace(redirect)
        localStorage.removeItem('loginRedirect')
      }
    },
    // 由组件实现
    loginWithCode () {},

    // 请求完成的回调
    loginCallback (res) {
      if (res.status == 200) {
        setToken(res.data.token)
        this.login(true)
      } else {
        this.login(false)
      }
    },
    loginFail () {
      this.login(false)
    }
  },
}
</script>