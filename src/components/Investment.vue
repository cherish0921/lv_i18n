<template>
  <div id="Investment">
     <router-view/>
<!--    @touchstart="movestart" @touchmove="moving" @touchend="moveend" -->
  <div class="Investment firstlev">
    <div class="mainhead">
    <div class="title">
      {{$t('投资')}}
    </div>
    <ul class="header-list">
      <li :class="{nowpage:(limodule==1)}" @touchend="limodulefun(1)"><span>{{$t('全部')}}</span></li>
      <li :class="{nowpage:(limodule==2)}" @touchend="limodulefun(2)"><span>{{$t('平台标')}}</span></li>
      <li :class="{nowpage:(limodule==3)}" @touchend="limodulefun(3)"><span>{{$t('抵押标')}}</span></li>
    </ul>
   </div>
   <div class="investment-list">
     <nocontent v-if="(investList.length<=0)&&(investList_f.length<=0)"></nocontent>
     <div class="item"  :class="(Number(item.status)!=0)?'itemend':''"  :borrowid="item.id"  v-for="(item) in investList" :type="item.borrowTypeId"   :key="((limodule==1?'all':limodule==2?'pingtai':'diya'))+item.id " v-tap="{methods:itemfun,name:'tap',borrowCryptoId:item.borrowCryptoId,id:item.id,islave:true}" >
       <div class="hd_item">
          <span>
            <i :class="(item.borrowTypeId!=1?'':'diya')">{{$t(item.borrowTypeId!=1?('p标签'):('M标签'))}}</i>{{item.orderId}}
            <i v-if="item.borrowTypeId==1">{{item.nickname}}</i></span>
          <span>
            <img v-if="item.borrowTypeId==5" class="label" :src="'https://s.lendx.vip/static/test/images/Investment/'+($i18n.locale || 'en')+'_newbie.png'" alt="" srcset="">
            <img v-if="item.borrowTypeId==4" class="label" :src="'https://s.lendx.vip/static/test/images/Investment/'+($i18n.locale || 'en')+'_newbiepre.png'" alt="" srcset="">
            <img v-if="item.borrowTypeId==2" class="label" :src="'https://s.lendx.vip/static/test/images/Investment/'+($i18n.locale || 'en')+'_pre.png'" alt="" srcset="">
          </span>
        </div>
        <div class="newct_item">
            <div class="ct_item_list">
              <div class="ct_item_firstbox">
                <div class="ct_item_rates">
                  <span class="v_item_notfull">{{annualized(item.interestRates,item.borrowDays)}}</span>%</div>
                <div class="ct_item_label">{{$t('年化')}}</div>
              </div>
            </div>
            <div class="ct_item_list ct_item_listsec">
              <div class="ct_item_secbox">
                <div class="ct_item_borrowDays">{{item.borrowDays}}{{$t('天')}}</div>
                <div class="ct_item_label">{{$t('期限')}}</div>
              </div>
            </div>
            <div v-if="Number(item.status)==0" class="ct_item_three">
              <div class="ct_item_Amount">{{item.borrowAmount}}&nbsp;{{item.borrowCryptoCode}}</div>
              <div class="ct_item_label">{{$t('借入资产')}}</div>
            </div>
        </div>
        <div class="pg_item" v-if="Number(item.status)==0"><span class="lineout"><i class="linein gradient_line" :style="'width:'+(getboughtPercent(item.boughtAmount,item.borrowAmount)*7.6)+'rem;'" >&nbsp;</i></span><span>{{(getboughtPercent(item.boughtAmount,item.borrowAmount)*100).toFixed(2)}}%</span></div>
     </div>
     <p v-if="investList_f.length>0" style="margin-top:.6rem;color:#999;" @touchend="fullshowtoggle">{{$t((limodule==1)?'查看近期满标标的':((limodule==2)?'查看近期满标平台标':'查看近期满标抵押标'))}}&nbsp;&nbsp;<img v-if="!fullshow" src="https://s.lendx.vip/static/test/images/icon/arrow.png" alt="" srcset=""></p>
     <div v-if="fullshow" class="item"  :class="(Number(item.status)!=0)?'itemend':''"  :borrowid="item.id"  v-for="item in investList_f" :type="item.borrowTypeId"  :key="((limodule==1?'all':limodule==2?'pingtai':'diya'))+item.id" v-tap="{methods:itemfun,name:'tap',borrowCryptoId:item.borrowCryptoId,id:item.id}">
       <div class="hd_item">
        <span><i :class="(item.borrowTypeId!=1?'':'diya')">{{$t(item.borrowTypeId!=1?'p标签':'M标签')}}</i>{{item.orderId}}
        <i v-if="item.borrowTypeId==1">{{item.nickname}}</i></span>
         <span>
          <img v-if="item.borrowTypeId==5" class="label" :src="'https://s.lendx.vip/static/test/images/Investment/'+($i18n.locale || 'en')+'_newbie.png'" alt="" srcset="">
          <img v-if="item.borrowTypeId==4" class="label" :src="'https://s.lendx.vip/static/test/images/Investment/'+($i18n.locale || 'en')+'_newbiepre.png'" alt="" srcset="">
          <img v-if="item.borrowTypeId==2" class="label" :src="'https://s.lendx.vip/static/test/images/Investment/'+($i18n.locale || 'en')+'_pre.png'" alt="" srcset="">
         </span>
         </div>
       <table class="ct_item">
          <tr class="v_item">
              <td><span>{{annualized(item.interestRates,item.borrowDays)}}</span>%</td>
              <td>{{item.borrowDays}}{{$t('天')}}</td>
              <td v-if="Number(item.status)==0">{{item.boughtAmount}}&nbsp;{{item.borrowCryptoCode}}</td>
          </tr>
          <tr class="n_item">
              <td>{{$t('年化')}}</td>
              <td>{{$t('期限')}}</td>
              <td v-if="Number(item.status)==0">{{$t('借入资产')}}</td>
          </tr>
       </table>
       <div class="pg_item" v-if="Number(item.status)==0"><span class="lineout"><i class="linein gradient_line" :style="'width:'+(getboughtPercent(item.boughtAmount,item.borrowAmount)*7.6)+'rem;'" >&nbsp;</i></span><span>{{(getboughtPercent(item.boughtAmount,item.borrowAmount)*100).toFixed(1)}}%</span></div>
     </div>
   </div>
  </div>

  </div>
</template>

<script>
import { mapState,mapGetters,mapActions} from 'vuex'
import { googletotal } from "../util/watch.js";
import nocontent from './Investment/nocontent'
let interval
export default {
  name: "Investment",
  components:{
    'nocontent':nocontent
  },
  data() {
    return {
      busy:false,//是否在发起ajax请求 
      limodule:1,
      investList_a_f:[],//全部满标标
      investList_p_f:[],//满标平台标
      investList_m_f:[],//满标抵押标
      investList_a:[],//全部未满标标
      investList_p:[],//未满标平台标
      investList_m:[],//未满标抵押标
      fullshow:false,//满标是否显示
      nowpage_all:1,//全部标已加载了多少页
      nowpage_p:1,//平台标已加载了多少页
      nowpage_m:1,//抵押标已加载了多少
    };
  },
  beforeUpdate(){
    var style = document.createElement('style');
    style.type = 'text/css';
    style.appendChild(document.createTextNode('body .Investment .investment-list .itemend .ct_item{background-image: url("https://s.lendx.vip/images/Investment/'
    +(this.$i18n.locale || 'en')
    +'_full.png") !important;}'));
    //动态切换满标icon
    document.getElementsByTagName('head')[0].appendChild(style);
    document.querySelector('.firstlev').style.display=(window.location.href.indexOf('Investmentinfo')>0)?"none":"block"
  },
mounted(){
this.changeState({type:'isloading',value:true})
 var _self = this;
 this.changeState({type:'isloading',value:true})
  this.getmoredata()
//  let dom=document.querySelectorAll('.investment-list')[0]
  // function fnn(e) {
  //           if (window.scrollY >=dom.scrollHeight- window.screen.availHeight - 40) {
  //               if (!_self.busy) {
  //                   _self.busy = true;
  //                   _self.getmoredata();
  //               }
  //           }
  //       }
  if(this.$route.name=='Investment'){
    document.removeEventListener("scroll",_self.fnn)
    document.addEventListener("scroll",_self.fnn);
  }else{
    document.removeEventListener("scroll",_self.fnn)
  }
},
  computed: {
    //  ...mapState({module:'module'}), 
      investList_f:function(){
        return  (this.$data.limodule==1)?this.$data.investList_a_f:((this.$data.limodule==2)?this.$data.investList_p_f:this.$data.investList_m_f)
      },
      investList:function(){
         return (this.$data.limodule==1)?this.$data.investList_a:((this.$data.limodule==2)?this.$data.investList_p:this.$data.investList_m)
      }
  },
  methods: {
    annualized:function(interest,days){
      //根据天数来分别发挥年化或者日利率
         
         return (interest*100*360).toFixed(2)
    },
    fixFloat:function(val){
        return this.LEND.fixFloat(val)
    },
      getboughtPercent(a,b){
        return this.LEND.d(a,b)
      },
    getmoredata:function(){
       let investlistArr=[[this.$data.investList_a,this.$data.investList_a_f],
                           [this.$data.investList_p,this.$data.investList_p_f],
                           [this.$data.investList_m,this.$data.investList_m_f]]
        let nowpageArr=[,this.nowpage_all,this.nowpage_p,this.nowpage_m];
        if(nowpageArr[this.limodule]==-1){
          return false
        }
        if(((this.limodule==1)&&(this.nowpage_all==-1))||((this.limodule==3)&&(this.nowpage_m==-1))||((this.limodule==2)&&(this.nowpage_p==-1))){
          return false
        }

      //  setTimeout(()=>{
  this.$axios.get("/trade/invest/mobile/index", {
                    params: {
                        page: nowpageArr[this.limodule],
                        page_size: 10,
                        type: this.$data.limodule - 1
                    }
                }).then(result1 => {
                    switch (Number(result1.code)) {
                        case 2000:
                            switch (this.limodule) {
                                case 1:
                                    this.nowpage_all = (result1.data.length >= 10) ? (this.nowpage_all + 1) : -1;
                                    break;
                                case 2:
                                    this.nowpage_p = (result1.data.length >= 10) ? (this.nowpage_p + 1) : -1;
                                    console.log(this.nowpage_p);
                                    break;
                                case 3:
                                    this.nowpage_m = (result1.data.length >= 10) ? (this.nowpage_m + 1) : -1;
                                    break;
                                default:
                                    break;
                            }
                            result1.data.forEach(element => {
                                let secondi = (Number(element.status) == 0) ? 0 : 1
                                let tarArr = investlistArr[this.$data.limodule - 1][secondi]
                                let inclue = false;
                                for (let p = 0, lp = tarArr.length; p < lp; p++) {
                                    if (element.id == tarArr[p].id) {
                                        inclue = true
                                    }
                                }
                                if (inclue == false) {
                                    tarArr.push(element)
                                }

                            });
                            break;
                        default:
                            this.$toast(result1.message.toString())
                            break
                    }
                    this.changeState({type:'isloading',value:false})
                    this.busy = false;
                })
       // },200)
    },
    limodulefun:function(i){
       if(this.$data.limodule!=i){
        this.nowpage_all = 1;
        this.nowpage_p = 1;
        this.nowpage_m = 1;
        this.$data.limodule=i
        this.$data.fullshow=false
        this.investList_a_f = [],//全部满标标
        this.investList_p_f = [],//满标平台标
        this.investList_m_f= [],//满标抵押标
        this.investList_a= [],//全部未满标标
        this.investList_p= [],//未满标平台标
        this.investList_m= [],//未满标抵押标
        window.scrollTo(0,0);
        this.getmoredata();
        }
    },
    fullshowtoggle:function(){
      this.$data.fullshow=true
    },
    itemfun:function(s){
        if(s.islave){
          googletotal('invest_detail',this.$i18n.locale,'投资页列表点击');
        }
        this.$router.push({ name:'Investmentinfo', query:{id:s.id,borrowCryptoId:s.borrowCryptoId}})
      },
      fnn(e) {
        let _self=this
        let dom=document.querySelectorAll('.investment-list')[0]
        if(this.$route.name=='Investment'){
          if (window.scrollY >=dom.scrollHeight- window.screen.availHeight - 40) {
            if (!_self.busy) {
                _self.busy = true;
                _self.getmoredata();
            }
          }
        }
    },
      ...mapActions(['changeState'])
    },
    destroyed(){
        document.removeEventListener("scroll",this.fnn)
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="sass" scoped>
@import '../assets/css/mixin.scss'
@import "../components/Investment/Investment.scss"
</style>