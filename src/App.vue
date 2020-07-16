<template>
  <div id="app">
    <slide-verify ref="slideVerify" @success="onSuccess" @again="onAgain" @fulfilled="onFulfilled" @fail="onFail"
                  @refresh="onRefresh" :accuracy="1" :fnImgSrc="fnImgSrc" :fnVerify="fnVerify"></slide-verify>
    <div>{{msg}}</div>
    <button class="btn" @click="handleClick">在父组件可以点我刷新哦</button>
  </div>
</template>

<script>
import img0 from './assets/img.jpg'
import img1 from './assets/img1.jpg'
import img2 from './assets/img2.jpg'
import img3 from './assets/img3.jpg'
import img4 from './assets/img4.jpg'
import img5 from './assets/img5.jpg'
import CryptoJS from 'crypto-js'

export default {
  name: 'App',
  data () {
    return {
      msg: '',
      text: '向右拖动滑块填充拼图',
      imgs: [
        img0,
        img1,
        img2,
        img3,
        img4,
        img5,
      ],
      accuracy: 1, // 精确度小，可允许的误差范围小；为1时，则表示滑块要与凹槽完全重叠，才能验证成功。默认值为5
    }
  },
  methods: {
    onSuccess (times) {
      console.log('验证通过')
      this.msg = `login success, 耗时${(times / 1000).toFixed(1)}s`
    },
    onFail () {
      console.log('验证不通过')
      this.msg = ''
    },
    onRefresh () {
      console.log('点击了刷新小图标')
      this.msg = ''
    },
    onFulfilled () {
      console.log('刷新成功啦！')
    },
    onAgain () {
      console.log('检测到非人为操作的哦！')
      this.msg = 'try again'
      // 刷新
      this.handleClick()
    },
    handleClick () {
      this.$refs.slideVerify.reset()
      this.msg = ''
    },
    fnImgSrc (req) {
      let xx = req.x & 3
      console.log('fnImgSrc2 x ' + req.x + ' y ' + req.y + ' xx ' + xx)
      // return './dist/imgs/img1.f79e1d9ee0af670da4323145918396cf.jpg'
      let aesKey = '123432'
      let salt = '33adafds'
      let ver = 1
      let ts = (new Date().getTime())
      let data = { x: req.x, y: req.y, m: 'ss' }
      let content = salt + CryptoJS.AES.encrypt(JSON.stringify(data), aesKey).toString()
      let sign = CryptoJS.MD5(content + '&' + ts).toString()
      // Decrypt
      // var bytes = CryptoJS.AES.decrypt(content.substr(8), aesKey)
      // var originalText = bytes.toString(CryptoJS.enc.Utf8)
      // console.log(originalText)
      return 'http://localhost:8088/recMan/slideVerifyImg?s=' + content + '&v=' + ver + '&t=' + ts + '&_=' + sign
    },
    fnVerify (req) {
      console.log('fnVerify2 x ' + req.x + ' y ' + req.y)
      return req
    },
  },
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin-top: 60px;
  }

  .btn {
    margin-top: 20px;
    outline: 0;
    border: none;
    padding: 8px 15px;
    border-radius: 5px;
    color: #fff;
    background-color: #1890ff;
    cursor: pointer;
  }

  .btn:active {
    box-shadow: 1px 5px 0 rgba(0, 0, 0, 0.1) inset;
  }
</style>
