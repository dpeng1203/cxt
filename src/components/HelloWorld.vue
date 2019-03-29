<template>
  <div class="easy-pay">
    <div class="phone" v-if="is_phone && orderState == 1" >
        <div>
            <img src="../assets/333.png" alt="" class="photo-phone"> 
        </div>
        <div class="wrap">
          <div class="order">订单号：{{mch_order_id}}</div>
          <div class="money"><span class="pic">金额：</span>{{money}} <span class="pic"> 元</span></div>
          <div id="qrcode" ref="qrcode" class="code"></div>
          <!-- <div class="tip">请勿修改订单金额，否则则无法到账</div> -->
          <!-- <div class="tip1">正常到账时间为1-2分钟</div> -->
          <!-- <div class="money"><span class="pic">金额：</span>{{money}} <span class="pic"> 元</span></div> -->
          <p class="time">订单有效时间：{{minute}}分{{second}}秒</p>
          <!-- <div class="title" @click="hanleClick">打开支付宝</div> -->
          <!-- <div id="qrcode" ref="qrcode" class="code"></div> -->
          <div class="foot">
            <p class="foot-title">请按下面步骤：</p>
            <p>1.长按或截屏保存二维码到手机</p>
            <p>2.打开支付宝，扫一扫本地图片</p>
          </div>
        </div>
      </div>
      <div class="pc" v-if="!is_phone && orderState == 1">
        <!-- <div>
            <img src="../assets/333.png" alt="" class="photo"> 
        </div>
        <div class="wrapper1">
          <div class="dkzfb">请打开手机支付宝扫码支付</div>
          <div id="qrcode" ref="qrcode" class="code1"></div>
        </div> -->
        <div class="top-wrapper">
          <div class="top">
            <span class="top-title">你好，欢迎使用支付宝付款！</span> 
            <a href="https://help.alipay.com/lab/help_detail.htm?help_id=258086" class="topbar-link-last" target="_blank">常见问题</a>
          </div>
        </div>
        <div class="header-wrapper">
          <div class="header">
            <img src="../assets/333.png" alt="" class="logo"> 
            <span class="logo-title">我的收银台</span>
          </div>
        </div>
        <div class="content">
          <div class="cont-header">
            <div class="title-left">
              <p class="title-left-tip">正在使用即时到账交易 |   交易将在5分钟后关闭，请及时付款！</p>
              <div class="title-left-order">订单号：{{mch_order_id}}  </div>
            </div>
            <div class="order-money">
              <span class="order-money-number">{{money}}</span>
              <span>元</span>
            </div>
          </div>
          <div class="cont-cont">
            <div class="cont-cont-title">扫一扫付款（元）</div>
            <div class="cont-cont-money">{{money}}</div>
            <div class="qr-wrapper">
              <div id="qrcode" ref="qrcode" class="code1"></div>
              <div class="qr-foot">
                <img src="https://t.alipayobjects.com/images/T1bdtfXfdiXXXXXXXX.png" alt="">
                <div class="scan">
                  <p>打开手机支付宝</p>
                  <p>扫一扫继续付款</p>
                </div>
              </div>
            </div>
            <a href="https://mobile.alipay.com/index.htm" class="qrcode-downloadApp"  target="_blank" >首次使用请下载手机支付宝</a>
            <img src="https://t.alipayobjects.com/images/rmsweb/T1ASFgXdtnXXXXXXXX.png" class="qrguide-area-img" @click="active = !active" v-show="active == true">
            <img src="https://t.alipayobjects.com/images/rmsweb/T13CpgXf8mXXXXXXXX.png" class="qrguide-area-img " @click="active = !active" v-show="active == false">
          </div>
          
        </div>
        <div class="foot">
          <a href="https://fun.alipay.com/certificate/jyxkz.htm" target="_blank" class="icp">ICP证：沪B2-20150087</a>
          <img alt="合作机构" src="https://i.alipayobjects.com/e/201303/2R3cKfrKqS.png" >
        </div>
          
        
        
      </div>
    <div v-if="orderState == 3" class="order-state">订单已完成！！</div>
    <div v-if="orderState != 3 && orderState != 1" class="order-state">订单已失效！！</div>
  </div>   
</template>

<script>
import QRCode from 'qrcodejs2'
export default {
  name: 'HelloWorld',
  data () {
    return {
      orderState: 1,
      active: true,
      is_phone: true,
      mch_order_id: '',
      money: '',
      ts: '',
      link: '',
      minutes: 4,   // 此处修改分钟数量
      seconds: 59     // 此处修改秒数数量
    }
  },
  watch: {
      second: {
        handler (newVal) {
          this.num(newVal)
        }
      },
      minute: {
        handler (newVal) {
          this.num(newVal)
        }
      }
    },
    computed: {
      
      second: function () {
        return this.num(this.seconds)
      },
      minute: function () {
        return this.num(this.minutes)
      }
    },

  methods:{
    // hanleClick() {
    //   window.location.href = this.link
    // },
    num: function (n) {
        return n < 10 ? '0' + n : '' + n
      },
    add: function () {
      var _this = this
      let nowTime = (new Date()).getTime();
      console.log(nowTime)
      let mistiming = nowTime - this.ts
      mistiming = 300000 - mistiming
      console.log(mistiming)
      if( mistiming <= 0 ) {
        this.orderState = 2
        return
      }else{
        this.minutes = mistiming/1000/60
        console.log(this.minutes)
        this.minutes = parseInt(this.minutes)
        this.seconds = parseInt(mistiming/1000 - (this.minutes*60))
      }
      console.log(this.minutes,this.seconds)
      var time = window.setInterval(function () {
        if (_this.seconds === 0 && _this.minutes !== 0) {
          _this.seconds = 59
          _this.minutes -= 1
        } else if (_this.minutes === 0 && _this.seconds === 0) {
          _this.seconds = 0
          this.orderState = 2
          window.clearInterval(time)
        } else {
          _this.seconds -= 1
        }
      }, 1000)
    },

    qrcode (url) {
      let qrcode = new QRCode('qrcode',{
        width: 220, // 设置宽度，单位像素
        height: 220, // 设置高度，单位像素
        text: url   // 设置二维码内容或跳转地址
      })
    },
     //判断是否为手机端
    _isMobile() {
      let flag = navigator.userAgent.match(/(phone|pad|pod|iPhone|iPod|ios|iPad|Android|Mobile|BlackBerry|IEMobile|MQQBrowser|JUC|Fennec|wOSBrowser|BrowserNG|WebOS|Symbian|Windows Phone)/i)
      return flag;
    },
  },
  mounted() {
    if(this._isMobile()) {
      this.is_phone = true
    } else{
      this.is_phone = false
    }
    // this.add()
    let url = window.location.href.replace('#/','')
    this.link = url.split('link=')[1]

    let str = this.link.split('?')[1]
    console.log(str)
    let arr = str.split('&')
    console.log(arr)
    arr.forEach(ele => {
      let item = ele.split('=')
      if(item[0] == 'sys_order_id') {
        this.mch_order_id = item[1]
      }
      if(item[0] == 'money') {
        this.money = item[1]
      }
      if(item[0] == 'ts') {
        this.ts = item[1]
      }
    })
    this.add()
    // this.link = this.link.split('&')[0]
    console.log(this.link)
    this.$nextTick(() => {
          this.qrcode(this.link)
      })
    
  }
}
</script>

<style scoped>
.photo-phone{
   width: 3rem;
   margin: .5rem 0
}
.wrap{
  padding: .1rem 0 1.6rem 0;
  background: #ccc;
  /* color: #fff; */
  font-size: .5rem
}
.tip{
  font-weight: bold;
  color: #FF0000;
  font-size: 0.55rem
}
.tip1{
  font-size: .4rem;
  margin-top: .1rem;
}
.time{
  margin-top: .1rem;
  font-size: .4rem
}
.title{
  display: inline-block;
  width: 6rem;
  height: 1.2rem;
  line-height: 1.2rem;
  margin-top: .2rem;
  border-radius: 0.6rem;
  background: #fff;
  color: #409EFF;
}
.money{
  font-size: .8rem;
  color: #FF0000;
  margin-top: .2rem;
}
.pic{
  font-size: .4rem
}
.order{
  margin-top: .4rem;
  font-size: .4rem
}
.tip{
  margin-top: .1rem;
}
.code{
  padding: .4rem;
  background: #fff;
  display: inline-block;
  margin: .4rem 0
}
.foot{
  color: #FF0000;
  font-size: .5rem;
  margin-top: .4rem
}
.photo{
  width: 180px;
  margin-top: 50px
}
.code1{
  /* width: 200px; */
  /* height: 200px; */
  display: inline-block;
  /* padding: 20px; */
  background: #fff;
}
.foot-title{
  font-weight: bold
}


.pc{
  width: 100%;
  background: #eff0f1;
  min-height: 100vh;
  color: #4D4D4D
}
.top-wrapper{
  height: 26px;
  line-height: 26px;
  background: url(https://i.alipayobjects.com/e/201305/OzLou0mHd.png) repeat-x 0 0;
  color: #fff;
  font-size: 12px;
}
.top{
  width: 900px;
  margin: 0 auto;
  text-align: right;
}
.top-title{
  padding: 4px 10px;
}
.topbar-link-last{
  color: #fff;
  padding: 4px 15px;
  background: url(https://i.alipayobjects.com/e/201305/OzUPukVET.png) no-repeat 0 ;
}

.header-wrapper{
  height: 60px;
  background-color: #fff;
  border-bottom: 1px solid #d9d9d9;
}
.header{
  width: 900px;
  margin: 0 auto;
  text-align: left;
}

.logo{
  width: 110px;
  float: left;
  margin-top: 10px
}

.logo-title{
  font-size: 16px;
  font-weight: normal;
  font-family: "Microsoft YaHei",微软雅黑,"宋体";
  border-left: 1px solid #676d70;
  color: #676d70;
  height: 20px;
  float: left;
  margin-top: 15px;
  margin-left: 10px;
  padding-top: 10px;
  padding-left: 10px;
}
.content{
  width: 900px;
  margin: 0 auto;
  text-align: left;
}
.cont-header{
  display: flex;
  align-items: center;
}
.title-left{
  flex: 1;
  padding-left: 30px 
}
.title-left-tip{
  font-size: 12px;
  padding-top: 40px;
}
.title-left-order{
  font-weight: 600;
  font-size: 14px;
  padding-top: 15px;
  padding-bottom: 20px
}
.order-money{
  font-size: 12px;
  padding-right: 30px
}
.order-money-number{
  font-size: 24px;
    color: #f60;
}

.cont-cont{
  min-height: 465px;
  background: #fff;
  border-top: 3px solid #ccc;
  border-bottom: 3px solid #ccc;
  text-align: center;
  font-size: 12px;
  padding: 60px 0 80px;
  position: relative;
}
.cont-cont-money{
  font-size: 26px;
    font-weight: 700;
    color: #f60;
    margin-top: 10px;
    margin-bottom: 20px
}
.qr-wrapper{
  width: 235px;
  margin: 0 auto;
  padding: 6px;
    border: 1px solid #d3d3d3;
    box-shadow: 1px 1px 1px #ccc;
    margin-bottom: 20px
}
.code1{
  width: 100%
}
.qrcode-downloadApp{
  color: #a6a6a6;
  text-decoration: underline;
}
.qrguide-area-img{
  position: absolute;
  top: 60px;
  left: 580px;
}
.foot{
  text-align: center;
  font-size: 12px;
  padding-bottom: 30px
}
.icp{
  display: block;
  padding: 30px 0 20px
}
.order-state{
  font-size: 18px;
  padding: 40px 0
}

</style>
