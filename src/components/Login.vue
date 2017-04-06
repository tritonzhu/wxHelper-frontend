<template>
  <div id="wrapper" class="text-center">
    <div id="loginBox" class="text-center">
      <img v-on:load="login" id="qrcode" src="/api/qrcode.png">
      <div id="tipText">
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
              this.tipText = '请在手机上确认登录'
              this.login()
            } else {
              window.location.reload()
            }
          },
          response => {
            console.log(response)
          }
        )
      }
    },
    data: function () {
      return {
        tipText: '请用微信扫描二维码登录'
      }
    }
  }
</script>

<style>
  #wrapper {
    width: 800px;
    height: 600px;
    padding-top: 100px;
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
