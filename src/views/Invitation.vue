<template>
  <div id="invitation">    
    <div class="func">
        <div class="func_left" @click="posterClick">
            <img class="func_left_img" src="../assets/images/codeBtnPoster.png" alt="海报">
            <span class="func_left_span">保存海报</span>
        </div>
        <!-- <div class="func_right" @click="linkClick">
            <img class="func_right_img" src="../assets/images/codeBtnShare.png" alt="分享">
            <span class="func_right_span">分享链接</span>
        </div> -->
    </div>
    <div class="shouji" v-if="tanchukaungShow">
        <div class="shouji_one"></div>
        <div class="shouji_two">
            <div class="shtw_three">长按保存图片，微信扫码识别</div>
            <div class="shtw_one">
                <div class="shtw_two_span" v-if="imgUrl==''">海报生成中...</div>
                <img class="shtw_one_img" :src="imgUrl" v-else>
            </div>
            <div class="shtw_two">
                <img class="shtw_two_img" src="../assets/images/quxiao.png" @click="guanbitankuang">
            </div>
        </div>
    </div> 
    <!-- <img style="width:100%;height:auto" :src="imgUrl" alt=""> -->
    <!-- <div class="headline">
        <span class="headline_span">孔雀计划券邀请码</span>
    </div>
    <div class="underline"></div> -->
      <div class="baoguo" ref="imageDom">
            <div class="present">
                <img class="present_img" src="../assets/images/codeCardBac.png" alt="背景图">
                <div class="present_one">
                    <span class="pron_spanOne">您的朋友</span>
                    <img class="pron_img" src="../assets/images/registerFawn.png" alt="头像" v-if="touxiang==''">
                    <img class="pron_img" :src="touxiang" alt="头像" v-else>
                    <span class="pron_spanTwo">{{mingzi}}</span>
                </div>
                <div class="present_two">
                    <span class="pt_spanOne" v-if="distingguish==2">邀请您入驻孔雀计划五星服务站 </span>
                    <span class="pt_spanOne" v-if="distingguish==1">邀请您入驻孔雀计划A级券商 </span>
                    <span class="pt_spanTwo">邀请码（{{yaoqingma}}）</span>
                </div>
            </div>
            <div class="card">
                <img class="card_img" src="../assets/images/codeShareBac.png" alt="背景图">
                <!-- <img class="card_bottle_img" src="../assets/images/feedingBottle.png" alt=""> -->
                <!-- <div class="card_words">
                    <div class="card_words_left">恭喜您获得：</div>
                  <div class="card_words_right">
                      <span class="card_words_right_one">享福计划之 娃哈哈·妙眠</span>
                      <span class="card_words_right_two">赠送10瓶 让您睡个好觉</span>
                  </div>
                </div> -->
                <div class="card_count">
                    <img id="erweima" class="card_count_img" :src="erweima" alt="二维码">
                </div>
            </div>
            <div class="title">
                <img class="title_left" src="../assets/images/codeIconLeft.png" alt="标志">
                <span class="title_center">银行VIP&白金卡推荐指定服务站</span>
                <img class="title_right" src="../assets/images/codeIconRight.png" alt="标志">
            </div>
            <div class="bank" v-if="listArr.length!=0">
                <div class="bank_page">
                    <div class="bapa" v-for="item in listArr">{{item}}</div>
                    <div class="bapa_message"></div>
                </div>
            </div>
            <div class="message"></div>
      </div>  
  </div>
</template>

<script>
import html2canvas from 'html2canvas';
export default {
  data() {
    return {
      distingguish:1,
      listArr:[],
      erweima:'',
      touxiang:'',
      mingzi:'',
      yaoqingma:'',
      imgUrl:'',
      tanchukaungShow:false
    };
  },
  methods:{
    // 保存海报点击
    posterClick(){
        let self = this;
        // self.$router.push({path:'R',query:{p:'1'}});
        html2canvas(this.$refs.imageDom).then(canvas => {
            // 转成图片，生成图片地址
            this.imgUrl = canvas.toDataURL("image/png");
        });
        self.tanchukaungShow = true
    },
    // 关闭弹框
    guanbitankuang(){
        let self = this;
        self.tanchukaungShow = false;
    },
    // 分享链接
    linkClick(){
        let self = this;
        // self.$router.push({path:'R',query:{p:'2'}});
        alert('使用微信打开，点击右上角分享')
    }
  },
  mounted(){
      let self = this;
      //console.log(self.$route.query);
      let parameter = self.$route.query;
      if(parameter.code == 1 || parameter.code == '1'){// A级商券推广码
        self.distingguish = 1;
        let para = `/cityPartnerMerchant/getPopularlizeBroker?no=${parameter.paraNumber}`;
        self.$axios.get(para)
        .then(resp => {
            // console.log(resp);
            if(resp.data.code == 0){
                let dataAll = resp.data.info;
                let strOne = '';
                let strTwo = '';
                let strThree = '';
                let strFour = '';
                // 二维码 
                if(dataAll.serviceRegisterPage && dataAll.serviceRegisterPage != undefined && dataAll.serviceRegisterPage != null && dataAll.serviceRegisterPage != ''){
                    strFour = 'data:image/png;base64,'+dataAll.serviceRegisterPage;
                }
                self.erweima = strFour;
                // 头像
                if(dataAll.yCityPartner.avatar && dataAll.yCityPartner.avatar != undefined && dataAll.yCityPartner.avatar != null && dataAll.yCityPartner.avatar != ''){
                    strOne = dataAll.yCityPartner.avatar;
                }
                self.touxiang = strOne;
                // 名字
                if(dataAll.yCityPartner.name && dataAll.yCityPartner.name != undefined && dataAll.yCityPartner.name != null && dataAll.yCityPartner.name != ''){
                    strTwo = dataAll.yCityPartner.name;
                }
                self.mingzi = strTwo;
                // 邀请码
                if(dataAll.yCityPartner.id && dataAll.yCityPartner.id != undefined && dataAll.yCityPartner.id != null && dataAll.yCityPartner.id != ''){
                    strThree = dataAll.yCityPartner.id;
                }
                self.yaoqingma = strThree;
                // 银行
                let yinhangArr = [];
                if(dataAll.bankInfo && dataAll.bankInfo != undefined && dataAll.bankInfo != null && dataAll.bankInfo != ''){
                    yinhangArr = dataAll.bankInfo;
                }
                self.listArr = yinhangArr;
            }
        }).catch(err => {
            // console.log(err);
            alert('请求出错，请稍后再试!');
        })
      }else if(parameter.code == 2 || parameter.code == '2'){// 商户推广码
        self.distingguish = 2;
        let paraOne = `/cityPartnerMerchant/getPopularlizeMerchant?no=${parameter.paraNumber}`;
        self.$axios.get(paraOne)
        .then(resp => {
            // console.log(resp);
            if(resp.data.code == 0){
                let dataAll = resp.data.info;
                let strOne = '';
                let strTwo = '';
                let strThree = '';
                let strFour = '';
                // 二维码 
                if(dataAll.serviceRegisterPage && dataAll.serviceRegisterPage != undefined && dataAll.serviceRegisterPage != null && dataAll.serviceRegisterPage != ''){
                    strFour = 'data:image/png;base64,'+dataAll.serviceRegisterPage;
                }
                self.erweima = strFour;
                // 头像
                if(dataAll.yCityPartner.avatar && dataAll.yCityPartner.avatar != undefined && dataAll.yCityPartner.avatar != null && dataAll.yCityPartner.avatar != ''){
                    strOne = dataAll.yCityPartner.avatar;
                }
                self.touxiang = strOne;
                // 名字
                if(dataAll.yCityPartner.name && dataAll.yCityPartner.name != undefined && dataAll.yCityPartner.name != null && dataAll.yCityPartner.name != ''){
                    strTwo = dataAll.yCityPartner.name;
                }
                self.mingzi = strTwo;
                // 邀请码
                if(dataAll.yCityPartner.id && dataAll.yCityPartner.id != undefined && dataAll.yCityPartner.id != null && dataAll.yCityPartner.id != ''){
                    strThree = dataAll.yCityPartner.id;
                }
                self.yaoqingma = strThree;
                // 银行
                let yinhangArr = [];
                if(dataAll.bankInfo && dataAll.bankInfo != undefined && dataAll.bankInfo != null && dataAll.bankInfo != ''){
                    yinhangArr = dataAll.bankInfo;
                }
                self.listArr = yinhangArr;
            }
        }).catch(err => {
            // console.log(err);
            alert('请求出错，请稍后再试!');
        })
      }
  }
};
</script>

<style lang="scss" scoped>
    @mixin box_one{
        display: flex;
        flex-direction: column;
    }
    @mixin box_two{
        display: flex;
        flex-direction: row;
        justify-content: center;
        align-items: center;
    }
    @mixin box_three{
        display: flex;
        justify-content: center;
        align-items: center;
    }
    @mixin box_four{
        display: flex;
        flex-direction: row;
        align-items: center;
    }
    @mixin box_five{
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
    }
    @mixin box_six{
        display: flex;
        flex-direction: row;
    }
    #invitation{
        @include box_one();
        width: 100%;
        height: auto;
        overflow-x: hidden;
        overflow-y: scroll;
        position: relative;
        .headline{//标题
            @include box_two();
            width:100%;
            height:px2rem(88);
            background:#FFFFFF;
            .headline_span{
            font-size:px2rem(36);
            font-weight:500;
            color:rgba(74,74,74,1);
            line-height:px2rem(25);
            }
        }
        .underline{//下划线
            width: 100%;
            height: px2rem(1);
            background: #EDEDED;
        }
        .baoguo{
            @include box_one();
            width: 100%;
            height: auto;
            .present{//顶部内容
                @include box_three();
                width: 100%;
                height: px2rem(478);
                position: relative;
                .present_img{
                    width: 100%;
                    height: px2rem(478);
                    position: absolute;
                    top: 0;
                    left: 0;
                }
                .present_one{
                    @include box_four();
                    width: auto;
                    height: px2rem(64);
                    position: absolute;
                    top: 22%;
                    left: 5.33%;
                    .pron_spanOne{
                        font-size:px2rem(28);
                        font-weight:500;
                        color:rgba(53,59,80,1);
                        line-height:px2rem(40);
                    }
                    .pron_img{
                        margin-left: px2rem(12);
                        width: px2rem(64);
                        height: px2rem(64);
                        border-radius: 50%;
                    }
                    .pron_spanTwo{
                        margin-left: px2rem(12);
                        font-size:px2rem(28);
                        font-weight:bold;
                        color:rgba(53,59,80,1);
                        line-height:px2rem(40);
                    }
                }
                .present_two{
                    @include box_four();
                    width: auto;
                    height: px2rem(40);
                    position: absolute;
                    top: 38%;
                    left: 5.33%;
                    .pt_spanOne{
                        font-size:px2rem(28);
                        font-weight:400;
                        color:rgba(53,59,80,1);
                        line-height:px2rem(40);
                    }
                    .pt_spanTwo{
                        margin-left: px2rem(8);
                        font-size:px2rem(28);
                        font-weight:bold;
                        color:rgba(53,59,80,1);
                        line-height:px2rem(40);
                    }
                }
            }
            .card{//卡片内容
                @include box_three();
                width: 100%;
                height: px2rem(560); 
                position: relative;
                .card_img{
                    width: 100%;
                    height: px2rem(560);
                    // position: absolute;
                    // top: 0;
                    // left: 0;
                    // z-index: -1;
                }
                .card_bottle_img{
                    position: absolute;
                    left: px2rem(30);
                    top: px2rem(56);
                    width: px2rem(66);
                    height: px2rem(158);
                }
                .card_words{
                    @include box_six();
                    width: px2rem(440);
                    height: px2rem(68);
                    position: absolute;
                    left: 4.27%;
                    top: px2rem(20);
                    @mixin add_words{
                        font-size:px2rem(24);
                        color:rgba(88,94,107,1);
                        line-height:px2rem(34);
                    }
                    .card_words_left{
                        @include add_words();
                        width: px2rem(155);
                        height: px2rem(68);
                        font-weight:400;
                    }
                    .card_words_right{
                        @include box_one();
                        @include add_words();
                        width: px2rem(305);
                        height: px2rem(68);
                        font-weight:bold;
                        .card_words_right_two{
                            margin-top: px2rem(6);
                        }
                    }
                }
                .card_count{
                    @include box_three();
                    width: px2rem(358);
                    height: px2rem(358);
                    background: #6AC6CD;
                    border-radius: px2rem(12);
                    position: absolute;
                    .card_count_img{
                        width: px2rem(356);
                        height: px2rem(356);
                    }
                }
            }
            .title{
                @include box_two();
                margin-top: px2rem(22);
                width: 100%;
                height: px2rem(44);
                .title_left{
                    width: px2rem(88);
                    height: px2rem(28);
                }
                .title_center{
                    font-size:px2rem(32);
                    font-weight:500;
                    color:rgba(53,59,80,1);
                    line-height:px2rem(44);
                }
                .title_right{
                    width: px2rem(88);
                    height: px2rem(28);
                }
            }
            .bank{
                @include box_three();
                margin-top: px2rem(28);
                width: 100%;
                height: auto;
                .bank_page{
                    @include box_five();
                    width: 91.47%;
                    height:auto;
                    background:rgba(251,251,251,1);
                    border-radius:px2rem(8);
                    border:px2rem(2) solid rgba(230,230,230,1);
                    .bapa{
                    padding: px2rem(24) 0 0 px2rem(46);
                    font-size:px2rem(27);
                    font-weight:400;
                    color:rgba(88,94,107,0.8);
                    line-height:px2rem(40);  
                    }
                    .bapa_message{
                        width: 100%;
                        height: px2rem(24);
                    }
                }
            }
            .message{//底部留白
                width: 100%;
                height: px2rem(136);
            }
        }        
            .func{//底部功能按钮
                @include box_two();
                width: 100%;
                height: px2rem(128);
                background:rgba(255,255,255,1);
                box-shadow:0 -6px 8px 0 rgba(173,173,176,0.1);
                position: fixed;
                bottom: 0;
                left: 0;
                z-index: 100;
                .func_left{
                    @include box_two();
                    width:91.47%;
                    height:px2rem(76);
                    background:linear-gradient(270deg,rgba(112,216,219,1) 0%,rgba(66,207,207,1) 100%);
                    border-radius:px2rem(4);
                    .func_left_img{
                        width: px2rem(36);
                        height: px2rem(36);
                    }
                    .func_left_span{
                        margin-left: px2rem(6);
                        font-size:px2rem(28);
                        font-weight:400;
                        color:rgba(255,255,255,1);
                        line-height:px2rem(40);
                    }
                }
                // .func_right{
                //     @include box_two();
                //     margin-left: px2rem(56);
                //     width:px2rem(258);
                //     height:px2rem(76);
                //     background:linear-gradient(270deg,rgba(82,168,199,1) 0%,rgba(65,151,201,1) 100%);
                //     border-radius:px2rem(4);
                //     .func_right_img{
                //         width: px2rem(36);
                //         height: px2rem(36);
                //     }
                //     .func_right_span{
                //         margin-left: px2rem(6);
                //         font-size:px2rem(28);
                //         font-weight:400;
                //         color:rgba(255,255,255,1);
                //         line-height:px2rem(40);
                //     }
                // }
            }
            .shouji{//图片弹框
                @include box_three();
                width: 100%;
                height: 100%;
                position: fixed;
                top: 0;
                left: 0;
                z-index: 200;
                .shouji_one{
                    width: 100%;
                    height: 100%;
                    background:rgba(0,0,0,0.5);
                }
                .shouji_two{
                    @include box_one();
                    width: 91.47%;
                    height: 80%;
                    position: absolute;
                    top: 10%;
                    left: 4.27%;
                    .shtw_three{
                        @include box_three();
                        width: 100%;
                        height: 5%;
                        font-size:px2rem(28);
                        font-weight:500;
                        color:rgba(53,59,80,1);
                        line-height:px2rem(40);
                        background: #FFFFFF;
                    }
                    .shtw_one{
                        width: 100%;
                        height: 90%;
                        background: #FFFFFF;
                        overflow-x: hidden;
                        overflow-y: scroll;
                        .shtw_two_span{
                            margin-top: 45%;
                            margin-left: 35%;
                            font-size:px2rem(28);
                            font-weight:500;
                            color:rgba(53,59,80,1);
                            line-height:px2rem(40);
                        }
                        .shtw_one_img{
                            width: 100%;
                            height: auto;
                        }
                    }
                    .shtw_two{
                        @include box_three();
                        width: 100%;
                        height: 5%;
                        .shtw_two_img{
                            width: px2rem(56);
                            height: px2rem(56);
                        }
                    }
                }
            }
    }
</style>
