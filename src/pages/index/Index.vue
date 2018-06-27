<template>
    <div>
        <div id="fullpage">
            <div class="section" id="page1">
                <Carousel autoplay :arrow="showArrow" :autoplay-speed="speed" v-model="valueCal" loop>
                    <!-- <CarouselItem v-for="(item,index) in picList"><div :style="'background-image: url('+item.url+')'" class="carousel-item"><a v-show="item.linkUrl!=''&&item.linkUrl!='1'" style="display:block;width:100%;height: 100%;" :href="item.linkUrl" target="_blank"></a></div></CarouselItem>-->
               
                <!-- 轮播图固定 -->
                <!-- <CarouselItem >
                    <div  class="carousel-item">
                        <img src="../../assets/img/banner.png" alt="">
                    </div>
                </CarouselItem> -->

                <!-- 可通过后台上传图片 -->
                <CarouselItem v-for="(item,index) in imgList" :key="index">
                    <!-- <div class="demo-carousel">
                        <div class='CarouselText'>
                            <h3>{{item.name}}</h3>
                            <p>{{item.remark}}</p>
                        </div>
                        <img  :src=item.url>
                    </div> -->
                     <div  class="carousel-item">
                        <img  :src=item.url>
                    </div>
                </CarouselItem>
               
                </Carousel>
            </div>
            <div class="section" id="page2">
                <div class="page2nav">
                    <ul class="brclearfix">
                        <li v-for="(item,index) in indexBtn"   :key="index" @click="addClass(index)" :class="{'active':index==choseBtn,'ivu-btn-ghost':index!=choseBtn}">{{item.text}}</li>
                    </ul>
                </div>
                <div class="ptjy">
                    <Table id="USDT" v-show="true" :columns="coins.columns"  :data="dataIndex" style='width:100% !important;' :no-data-text="$t('common.nodata')" ></Table>
                </div>
            </div>
            <div class="section" id="page5">
                <div class="mainPage5">
                    <div class="page5Left">
                        <img src="../../assets/img/iphoneBig.png" alt="">
                        <!-- <img src="../../assets/images/iosdown.png" alt=""> -->
                    </div>
                    <div class="page5Right">
                        <h3>{{$t("description.message5")}}</h3>
                         <h1>{{$t("description.message4")}}</h1>
                        <!-- <h3>{{$t("description.message5")}}</h3> -->
                        <ul>
                            <li>
                                <a @click="donwload(0)"><img src="../../assets/img/iphone.png" alt=""></a>
                            </li>
                            <li>
                                <a @click="donwload(1)"><img src="../../assets/img/andriod.png" alt=""></a>
                            </li>
                        </ul>
                    </div>
                </div>
               
            </div>
            <div class="section" id="page4">
                <ul>
                    <li>
                        <img src="../../assets/img/icon1.png" alt="">
                        <h6>{{$t("description.message6")}}</h6>
                        <p>{{$t('description.message1')}}</p>
                    </li>
                    <li style="margin:0 2%">
                        <img src="../../assets/img/icon2.png" alt="">
                        <h6>{{$t("description.message7")}}</h6>
                        <p>{{$t("description.message2")}}</p>
                    </li>
                    <li >
                        <img src="../../assets/img/icon3.png" alt="" >
                        <h6>{{$t("description.message8")}}</h6>
                        <p>{{$t("description.message3")}}</p>
                    </li>
                </ul>
            </div>
        
        </div>
      <div id="onlineservice">
        <!-- <a href="http://kefu.caymanex.pro:80/im/text/15FwEk.html" target="_blank"></a> -->
      </div>
    </div>
</template>
<script>
var Stomp = require('stompjs');
var SockJS = require('sockjs-client');
var moment = require('moment');
import SvgLine from '@components/exchange/SvgLine.vue';

export default {
    components:{SvgLine},
    data() {
        let self = this;
        return {
            CNYRate:null,
            dataIndex: [],
            imgList:[],
            //当前市场上的交易币种，按交易对分
            coins: {
                _map: [],
                USDT: [],
                BTC: [],
                ETH: [],
                favor: [],
                columns: [
                    {
                        title: self.$t('service.COIN'),
                        align: 'center',
                        key: 'coin',
                        render: function(h, params) {
                            const className = params.row.href + 'icon' + ' indexicon';
                            let iconName = '';
                            if (params.row.coin == 'BTC') {
                                return h('div', [
                                    h('img', {
                                        attrs: {
                                        src:require('../../assets/images/btc.png')
                                        },
                                    } ),
                                    h('span', {}, params.row.coin),
                                ])
                            } else if (params.row.coin == 'ETH') {
                                return h('div', [
                                    h('img', {
                                        attrs: {
                                        src:require('../../assets/images/eth.png')
                                        },
                                    } ),
                                    h('span', {}, params.row.coin),
                                ])
                            } else if (params.row.coin == 'BCH') {
                                return h('div', [
                                    h('img', {
                                        attrs: {
                                        src:require('../../assets/images/BCH.png')
                                        },
                                    } ),
                                    h('span', {}, params.row.coin),
                                ])
                             } else if (params.row.coin == 'BCH') {
                                return h('div', [
                                    h('img', {
                                        attrs: {
                                        src:require('../../assets/images/BCH.png')
                                        },
                                    } ),
                                    h('span', {}, params.row.coin),
                                ])
                             } else if (params.row.coin == 'LTC') {
                                return h('div', [
                                    h('img', {
                                        attrs: {
                                        src:require('../../assets/images/LTC.png')
                                        },
                                    } ),
                                    h('span', {}, params.row.coin),
                                ])
                              } else  {
                                 return h('div', [
                                    h('span', {}, params.row.coin),
                                ])
                             }  
                           
                        },
                    },
                    {
                        title:self.$t('service.NewPrice'),
                        align: 'center',
                        key: 'price',
                        width: '190',
                        sortable: true,
                        sortMethod: function(a, b, type) {
                            let a1 = parseFloat(a);
                            let b1 = parseFloat(b);
                            if (type == 'asc') {
                                return a1 - b1
                            } else {
                                return b1 - a1
                            }
                        },
                        render: function(h, params) {
                            var rmb = self.round(self.mul(params.row.price, 6.5), 2);
                            if (self.CNYRate != null) rmb = self.round(self.mul(params.row.price, self.CNYRate), 2);
                            const isgreen = parseFloat(params.row.rose) < 0 ? 'none' : 'inline-block';
                            const nogreen = parseFloat(params.row.rose) < 0 ? 'inline-block' : 'none';
                            return h('div', [
                                h('span', {
                                }, '$' + params.row.price + ' /￥' + rmb),
                                h('Icon', {
                                    props: {
                                        type: "arrow-up-c",
                                    },
                                    style: {
                                        display: isgreen,
                                        fontSize: '16px',
                                        marginLeft: '5px',
                                        verticalAlign: 'middle',
                                    },
                                    class: {
                                        green: true
                                    }
                                }, '↑'),
                                h('Icon', {
                                    props: {
                                        type: "arrow-down-c",
                                    },
                                    style: {
                                        display: nogreen,
                                        fontSize: '16px',
                                        marginLeft: '5px',
                                        verticalAlign: 'middle',
                                    },
                                    class: {
                                        red: true
                                    }
                                }, '↓'),
                            ])
                        },
                    },
                    {
                        title:self.$t('service.ExchangeNum'),
                        align: 'center',
                        key: 'volume',
                        sortable: true,
                        sortMethod: function(a, b, type) {
                            let a1 = parseFloat(a);
                            let b1 = parseFloat(b);
                            if (type == 'asc') {
                                return a1 - b1
                            } else {
                                return b1 - a1
                            }
                        }
                    },
                    {
                        title: self.$t('service.OpenPrice'),
                        align: 'center',
                        key: 'open',
                        sortable: true,
                        sortMethod: function(a, b, type) {
                            let a1 = parseFloat(a);
                            let b1 = parseFloat(b);
                            if (type == 'asc') {
                                return a1 - b1
                            } else {
                                return b1 - a1
                            }
                        }
                    },
                    {
                        title: self.$t('service.Change'),
                        align: 'center',
                        key: 'rose',
                        sortable: true,
                        sortMethod: function(a, b, type) {
                            let a1 = a.replace(/[^\d|.|-]/g, '') - 0
                            let b1 = b.replace(/[^\d|.|-]/g, '') - 0
                            if (type == 'asc') {
                                return a1 - b1
                            } else {
                                return b1 - a1
                            }
                        },
                        render: (h, params) => {
                            const row = params.row;
                            const className = parseFloat(row.rose) < 0 ? 'red' : 'green';
                            return h('span', {
                                attrs: {
                                    class: className
                                }
                            }, row.rose)
                        }
                    },
                    {
                        title: self.$t('service.PriceTrend'),
                        render:function(h,params){
                            return h(SvgLine,{
                                props:{
                                    values:params.row.trend,
                                    rose:params.row.rose,
                                    width:100,
                                    height:40
                                }
                            });
                        }
                    },
                    {
                        title: self.$t('service.Operate'),
                        align: 'center',
                        key: 'buyBtn',
                        render: function(h, params) {
                            return h('p', [
                                h('a', {
                                    style: {
                                        color: '#fff'
                                    },
                                    on: {
                                        click: function() {
                                            self.$router.push('/exchange/' + params.row.href);
                                        }
                                    }

                                }, [
                                        h('Button', {
                                            props: {
                                                type: 'primary',
                                                long: true,
                                                // shape: "circle",
                                            },
                                            style: {
                                                marginRight: '8px',
                                                width: '80%',
                                                background: '#27abe2',
                                                border: '#27abe2',
                                            },
                                        }, self.$t('service.Exchange'))
                                    ]),
                            ]);
                        }
                    },
                ]
            },
            indexBtn: [
                {
                    text: this.$t('service.USDT')
                },
                {
                    text: this.$t('service.BTC')
                },
                {
                    text: this.$t('service.ETH')
                },
                {
                    text: this.$t('service.CUSTOM')
                },
            ],
            choseBtn: 0,
            valueCal: 0,
            showArrow: 'never',
            speed: 5000,
            symbol: '',
            usdtData: [],
            usdtList: [

            ],
            btcList: [

            ],
            ethList: [

            ],
            picList:[
              // {
              //   url:'../../assets/images/banner1.jpg'
              // },{
              //   url:'../../assets/images/banner2.jpg'
              // }
            ]
        }
    },
    created: function(){
        this.init();
        this.getImg();
    },
    computed: {
        'isLogin': function() {
            return this.$store.getters.isLogin;
        },
        'lang': function () {
          return this.$store.state.lang;
        }
    },
    watch:{
        'lang':function () {
          this.updateLangData();
        }
    },
    mounted: function() {
        this.getCNYRate();
        this.getSymbol();
    },
    methods: {
        updateLangData(){
          this.indexBtn = [
            {
              text: this.$t('service.USDT')
            },
            {
              text: this.$t('service.BTC')
            },
            {
              text: this.$t('service.ETH')
            },
            {
              text: this.$t('service.CUSTOM')
            },
          ];

          this.coins.columns[0].title = this.$t('service.COIN');
          this.coins.columns[1].title = this.$t('service.NewPrice');
          this.coins.columns[2].title = this.$t('service.ExchangeNum');
          this.coins.columns[3].title = this.$t('service.OpenPrice');
          this.coins.columns[4].title = this.$t('service.Change');
          this.coins.columns[5].title = this.$t('service.PriceTrend');
          this.coins.columns[6].title = this.$t('service.Operate');
        },
      openActivity(url){
        console.log(url);
      },
        init() {
            this.$store.commit('navigate','nav-index');
            this.$store.state.HeaderActiveName = '1-1';
            this.loadPicData();
            this.addClass(0);
        },
        getCNYRate(){
            this.$http.post(this.host + '/market/exchange-rate/usd-cny').then(response => {
              var resp = response.body;
              this.CNYRate = resp.data;
            });
        },
        donwload(type){
          const title = this.$t('common.tip');
          const content = '<p>'+this.$t('common.expect')+'</p>';
          this.$Modal.info({
            title: title,
            content: content,
            closable: true
          });
        },
        loadPicData(){
            let param = {};
            param["sysAdvertiseLocation"] = 1;
            this.$http.post(this.host + '/uc/ancillary/system/advertise', param).then(response => {
              var result = response.body;
              if(result.code == 0 && result.data.length > 0){
                this.picList = result.data;
              }
            });
        },
        getCoin(symbol) {
            return this.coins._map[symbol];
        },
        startWebsock() {
            var stompClient = null;
            var that = this;
            var socket = new SockJS(that.host + that.api.market.ws);
            stompClient = Stomp.over(socket);
            stompClient.debug = false;
            stompClient.connect({}, function(frame) {
                //订阅价格变化消息
                stompClient.subscribe('/topic/market/thumb', function(msg) {
                    var resp = JSON.parse(msg.body);
                    var coin = that.getCoin(resp.symbol);
                    if (coin != null) {
                        coin.price = (resp.close).toFixed(2);
                        coin.rose = resp.chg > 0 ? ('+' + (resp.chg * 100).toFixed(2) + '%') : ((resp.chg * 100).toFixed(2) + '%');
                        coin.close = (resp.close).toFixed(2);
                        coin.high = (resp.high).toFixed(2);
                        coin.low = (resp.low).toFixed(2);
                        coin.turnover = parseInt(resp.volume);
                    }
                });
            });
        },
        round(v, e) {
            var t = 1;
            for (; e > 0; t *= 10, e--);
            for (; e < 0; t /= 10, e++);
            return Math.round(v * t) / t;
        },
        mul(a, b) {
            var c = 0,
                d = a.toString(),
                e = b.toString();
            try {
                c += d.split(".")[1].length;
            } catch (f) { }
            try {
                c += e.split(".")[1].length;
            } catch (f) { }
            return Number(d.replace(".", "")) * Number(e.replace(".", "")) / Math.pow(10, c);
        },
        addClass(index) {
            this.choseBtn = index;
            if (index == 0) {
                this.dataIndex = this.coins.USDT
            } else if (index == 1) {
                this.dataIndex = this.coins.BTC
            } else if (index == 2) {
                this.dataIndex = this.coins.ETH
            } else if (index == 3) {
                if (this.isLogin) {
                    this.dataIndex = this.coins.favor;
                } else {
                    this.$router.push('/login');
                }
            }
        },
        getSymbol() {
            this.$http.post(this.host + this.api.market.thumbTrend, {}).then(response => {
                var resp = response.data;
                for (let i = 0; i < resp.length; i++) {
                    var coin = resp[i];
                    
                    coin.price = resp[i].close;
                    coin.rose = resp[i].chg > 0 ? ('+' + (resp[i].chg * 100).toFixed(2) + '%') : ((resp[i].chg * 100).toFixed(2) + '%');
                    coin.coin = (resp[i].symbol).split('/')[0];
                    coin.base = (resp[i].symbol).split('/')[1];
                    coin.href = (coin.coin + '_' + coin.base).toLowerCase();
                    coin.isFavor = false;
                    this.coins._map[coin.symbol] = coin;
                   
                    this.coins[coin.base].push(coin);
                }
                if (this.isLogin) {
                  this.getFavor();
                }
                this.startWebsock();
            })
        },
        getFavor() { //查询自选
            this.$http.post(this.host + this.api.exchange.favorFind, {}).then(response => {
                var resp = response.body;
                for (var i = 0; i < resp.length; i++) {
                    var coin = this.getCoin(resp[i].symbol);
                    this.coins.favor.push(coin);
                }
            });
        },
        // banner广告
        getImg(){
        
            this.$http.post(this.host+'/uc/ancillary/system/advertise',{sysAdvertiseLocation:1}).then(response => {
                var resp=response.body.data;
                this.imgList=resp;
            })
        }

    }

}
</script>
<style>


.ptjy .ivu-table-body table,.ptjy .ivu-table-header table{
    width:100% !important;
}
.ptjy .ivu-table-body table .ivu-table-row td img,.ptjy .ivu-table-header table .ivu-table-row td img{
    display:inline-block;
    vertical-align:middle;
    margin-right:6px;
}
.indexicon {
    width: 25px;
    height: 25px;
    background-image: url(../../assets/images/gccicon.png);
    background-size: 100% 100%;
    display: inline-block;
    vertical-align: middle;
    margin-right: 10px;
}

.gcc_usdticon {
    background-image: url(../../assets/images/gccicon.png);
}

.btc_usdticon {
    background-image: url(../../assets/images/btcicon.png);
}

.eth_usdticon {
    background-image: url(../../assets/images/ethicon.png);
}

.green {
    color: #589065 !important;
}

.red {
    color: #AE4E54 !important;
}

.brclearfix:after {
    content: '';
    display: block;
    height: 0;
    overflow: hidden;
    clear: both;
}

#page2 {
    background: #fff;
    height: auto;
    /* min-height: 320px; */
    margin:70px 0 100px;
    min-width:1200px;
}

.page2nav {
    padding: 0 10%;
    /* background: #1b262d; */
    line-height: 60px;
    font-size: 20px;
}

.page2nav li {
    float: left;
    cursor: pointer;
    color: #A3A1AB;
    list-style:none;
    padding:0 60px;
    background-color:#F4F4F4;
    line-height:50px;
}

.page2nav li.active {
    background: #252e3e;
    color: #fff;
    position: relative;
    background-color:#419AF6;
    border-top-left-radius: 4px;
    border-top-right-radius: 4px;
}

.page2nav li.active::after {
    content: "";
    border-top: 10px solid #252e3e;
    border-left: 15px solid transparent;
    border-right: 15px solid transparent;
    position: absolute;
    bottom: -10px;
    left: 43%;
}

#fullpage {
    background: #fff;
}

.section {
    /* height: 575px; */
    text-align: center;
    color: #fff;
}

.carousel-item{
  background-repeat: no-repeat;
  background-position: center;
  /* height: 575px; */
  background-size: cover;
}
.carousel-item img{
    width:100%;
    /* min-height:390px; */
    height:575px;
}

.demo-carousel1 {
    /* background: url(../../assets/images/banner1.jpg) no-repeat center; */
    height: 575px;
    background-size: cover;
}

.demo-carousel2 {
    /* background: url(../../assets/images/banner2.jpg) no-repeat center; */
    height: 575px;
    background-size: cover;
}

.demo-carousel-btn {
    width: 100%;
    height: 100%;
    padding-top: 345px;
}

.demo-carousel1 a {
    display: inline-block;
    width: 250px;
    height: 55px;
    margin: 0 15px;
}

.purchase {
    background: url(../../assets/images/purchase.png) no-repeat;
}

.register {
    background: url(../../assets/images/register.png) no-repeat;
}

.ptjy {
    height: 100%;
    margin:0 10% 30px;
    /* padding-top: 25px; */
}

.usdt {
    float: left;
    width: 100%;
}

.usdt_icon {
    float: left;
    width: 18%;
    height: 290px;
    background: #1d293a;
    padding-top: 125px;
    margin: 5px;
}

.ptjy ul {
    float: left;
    width: 80%;
}

.ptjy ul li {
    float: left;
    width: 15%;
    height: 140px;
    background: #1d293a;
    margin: 5px;
    cursor: pointer;
}

.ptjy ul li a {
    color: #fff;
}

.ptjy ul li:hover {
    box-shadow: 0 8px 16px rgba(29, 41, 255, 0.4)
}

.ptjy ul li.rise label {
    color: #32b114;
}

.ptjy ul li.drop label {
    color: #e01a1a;
}

.ptjy ul li span,
.ptjy ul li label {
    display: block;
    padding: 10px 0;
    cursor: pointer;
}

.ptjy ul li label img {
    vertical-align: middle;
}

.btc,
.eth {
    float: left;
    width: 100%;
    margin-top: 10px;
}

.btc_icon,
.eth_icon {
    float: left;
    width: 18%;
    height: 140px;
    background: #1d293a;
    padding-top: 50px;
    margin: 5px;
}

#nav {
    position: fixed;
    right: 10%;
    top: 50%;
    z-index: 100;
}

#nav ul li {
    display: block;
    /* width: 120px; */
    height: 25px;
    margin: 7px;
    position: relative;
    padding-right: 20px;
    text-align: right;
    color: #fff;
}

#nav ul li span {
    display: none;
}

#nav ul li a {
    top: 2px;
    right: 2px;
    width: 8px;
    height: 8px;
    background: url(../../assets/images/page.png) no-repeat;
    position: absolute;
    z-index: 1;
}

#nav ul li a:hover,
#nav ul li a.active {
    top: 0;
    right: -3px;
    width: 18px;
    height: 18px;
    background: url(../../assets/images/page_active.png) no-repeat;
    position: absolute;
    z-index: 1;
}

#page3 {
    position: relative;
    color: #979797;
    background: url(../../assets/images/section3.png) no-repeat center;
}

#page3 label {
    position: absolute;
    top: 30%;
    left: 20%;
    font-size: 30px;
}

@-webkit-keyframes fadeinB {
    0% {
        top: 50%;
        opacity: 0;
    }
    100% {
        top: 30%;
        opacity: 1;
    }
}

@keyframes fadeinB {
    0% {
        top: 50%;
        opacity: 0;
    }
    100% {
        top: 30%;
        opacity: 1;
    }
}

@-webkit-keyframes fadeinA {
    0% {
        top: 60%;
        opacity: 0;
    }
    100% {
        top: 40%;
        opacity: 1;
    }
}

@keyframes fadeinA {
    0% {
        top: 60%;
        opacity: 0;
    }
    100% {
        top: 40%;
        opacity: 1;
    }
}

#page3 p {
    position: absolute;
    top: 40%;
    left: 20%;
    font-size: 15px;
}

#page4 {
    position: relative;
    color: #8b8b8d;
    background: #f8f8fb;
    text-align:center;
}

#page4 label {
    position: absolute;
    top: 30%;
    left: 70%;
    font-size: 30px;
}

#page4 ul{
    display:inline-block;
    /* background: pink; */
    margin: 0 auto;
    padding: 70px 0;
    width:80%;
    max-width:1500px;
    min-width:1200px;
}

#page4 ul li {
    /* display: inline-block; */
    width: 32%;
    /* background: lavender; */
    float: left;
    padding:70px 30px 50px;
    border:1px solid #ddd;
}

#page4 h6{
    font-size:16px;
    color:#0C0C0C;
    height:40px;
    line-height:40px;
    margin:10px 0;
}
#page4 p {
    font-size: 15px;
    margin: 20px 0;
    text-align: left;
    min-height:100px;
}

#page5 {
    position: relative;
    color: #fff;
     /* background-image: url(../../assets/images/gccicon.png); */
    background-size: 100% 100%;
    background-image:url(../../assets/images/transparenet.png);
    padding: 0 10%;
    text-align: center;
    padding: 40px 0px 0;
    margin:70px 0;
}

#page5 h1 {
    font-size: 40px;
    font-weight: normal;
    color:#000;
    margin-bottom:40px;
     max-width:680px;
}

#page5 h3 {
    font-size: 22px;
    font-weight: normal;
    line-height: 30px;
    color:#000;
    max-width:680px;
}

#page5 ul li {
    display: inline-block;
    margin-right:20px;
}
.mainPage5{
    width:1200px;
    margin:0 auto;
    overflow:hidden;
}
.page5Left{
    display:inline-block;
    vertical-align:middle;
    margin-right:150px;
}
.page5Right{
    display:inline-block;
    vertical-align:middle;
    text-align:left
}

.page5_title {
    font-size: 30px;
    line-height: 200px;
}

.page5_content {
    text-align: left;
}

.page5_content ul {
    float: left;
    width: 27%;
}

.page5_content ul li {
    height: 70px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

.page5_content ul li span {
    margin-right: 10px;
}

.news_1 {
    color: #202b3c;
    font-size: 12px;
}

.news_2 {
    color: #505c6f;
    font-size: 13px;
}

.news_3 {
    color: #fff;
    font-size: 18px;
}

.news_title {
    color: #fff;
    font-size: 20px;
}

.news_date {
    color: #505c6f;
}

.news_detail {
    color: #98999f;
    margin-top: 10px;
}
</style>
