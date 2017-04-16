<template>
  <div id="wrapper">
    <div id="loginBox">
      <img @load="login" id="qrcode" src="/api/qrcode.png">
      <div id="tipText">
        <p v-show="scanned">扫描成功</p>
        <p>{{ tipText }}</p>
      </div>
    </div>
    <router-view></router-view>
  </div>
</template>

<script>
  export default {
    name: 'login',
    methods: {
      login: function () {
        this.$http.get('/api/login').then(
          response => {
            let ret = response.data
            if (ret === '200') {
              this.$router.push({path: 'index'})
            } else if (ret === '408') {
              this.login()
            } else if (ret === '201') {
              this.scanned = true
              this.tipText = '请在手机上确认登录'
              this.login()
            } else {
              window.location.reload()
            }
          },
          response => {
            console.error('get response error')
            console.log(response)
          }
        )
      }
    },
    data: function () {
      return {
        tipText: '请用微信扫描二维码登录',
        scanned: false
      }
    }
  }
</script>

<style>
  body {
    margin: 0;
    padding: 0;
  }

  #wrapper {
    width: 800px;
    height: 500px;
    margin: 0;
    padding-top: 100px;
    text-align: center;
    background-image: url("/static/bg.jpg");
  }

  #loginBox {
    background-color: rgba(128, 128, 128, 0.7);
    width: 400px;
    height: 400px;
    padding-top: 50px;
    margin: auto auto;
  }

  #qrcode {
    width: 200px;
    height: 200px;
    opacity: 0.8;
  }

  #tipText {
    font-size: 20px;
    color: azure;
    margin-top: 30px;
  }
</style>
