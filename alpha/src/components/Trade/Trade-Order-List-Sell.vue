<template>
  <div class="exchange-left-side exchange-left-side-list sidebar mb-3" style="border-color:#e5e5e5;">
    <div style="background: #e5e5e5;">
      <div class="exchange-left-side-title">
        <div class="exchange-left-side-title-list">
          <div class="exchange-left-side-title-item" @click="showListTrader" :class="{active : activeListOderTrader == true}"> <img src="../../assets/images/frame1.png"></div>
          <div class="exchange-left-side-title-item" @click="showListTraderBuy" :class="{active : activeListOderTraderBuy == true}"><img src="../../assets/images/frame2.png"></div>
          <div class="exchange-left-side-title-item" @click="showListTraderSell" :class="{active : activeListOderTraderSell == true}"><img src="../../assets/images/frame3.png"></div>
        </div>
        <div class="group">
          {{$t('message.trade.group')}}
          <select @change="handleChange" v-model="selected">
            <option data-foo="1">1 {{$t('message.trade.decimals')}}</option>
            <option data-foo="2">2 {{$t('message.trade.decimals')}}</option>
            <option data-foo="3">3 {{$t('message.trade.decimals')}}</option>
            <option data-foo="4">4 {{$t('message.trade.decimals')}}</option>
            <option data-foo="5">5 {{$t('message.trade.decimals')}}</option>
            <option data-foo="6" value="6">6 {{$t('message.trade.decimals')}}</option>
          </select>
        </div>
      </div>
      <div class="order-lable scroll-table">
        <table class="clearfix exchange-left-side-up-down" :class="{ 'top-head-sell': (activeListOderTraderSell == true), 'top-head-buy': (activeListOderTraderBuy == true) }">
            <tr class="name">
              <th>{{$t('message.trade.price')}} (TRX)</th>
              <th>{{$t('message.trade.amount')}}({{ tradeToken.tokenName }})</th> 
              <th class="text-right">{{$t('message.trade.total')}} (TRX)</th>
            </tr>
        </table>
      </div>  
      <div class="scroll-table market-sell" :class="{ active: (activeListOderTraderSell == true) }" v-if="activeListOderTrader == true || activeListOderTraderSell == true">
        <div style="background: #fff;">
          
          <table class="clearfix exchange-left-side-up-down">
            <tr v-for="(item, idx) in  tradeOrderSellList[pair]" v-if="isShowOrderSellDetail(item, idx)"  class="cursor">
              <td @click="setTradePriceToken(item.price)">
                <div class="d-flex align-items-center" style="position: relative;margin-left: 2px;">
                <span style="min-width: 8px;position: absolute;left: -18px;top:1px;">
                  <img v-if="isYourOrder(item)" src="../../assets/images/my-order-icon.png">
                </span>
                <span class="down">{{ (item.isDefault == false) ? numberWithCommas(item.price , tradeSortByDecimal) : '-'}}</span>
              </div>
              </td>
              <td @click="setTradeOrderSellActive(item)">{{ (item.isDefault == false) ? numberWithCommas(item.tokens) : '-'}}</td> 
              <td @click="setTradeOrderSellActive(item)" class="text-right"><span>{{ (item.isDefault == false) ? numberWithCommas(item.total, 6) : '-'}}</span></td>
            </tr>
          </table>
      </div>
      </div>
    </div>
     <div style="background: #e5e5e5;">
        <!-- <tr class="exchange-left-side-title-up">
          <td colspan="3"> -->
           <div class="exchange-left-side-title-up d-flex align-items-center justify-content-between">
            <div class="d-flex align-items-baseline" >
              <span :class="{down:(isMarketDown() == true ) , up :(isMarketUp() == true)}">{{ numberWithCommas(tradeToken.lastPrice, 6) }}</span>
              <span v-if="isMarketUp() == true" class="up mr-1 ml-1"><i class="fa fa-long-arrow-up" aria-hidden="true"></i></span>
              <span v-if="isMarketDown() == false && isMarketUp() == false" class="mr-1 ml-1"> </span>
              <span v-if="isMarketDown() == true" class="down mr-1 ml-1"><i class="fa fa-long-arrow-down" aria-hidden="true"></i></span>
              <span>${{numberWithCommas(tradeToken.lastPrice * priceUsdOnTrx, 6)}}</span>
              </div>
              <img src="../../assets/images/signal.png">
            </div>
         <!--  </td>
        </tr> -->
        <div style="background: #e5e5e5;">
          <div class="scroll-table market-buy" :class="{ active: (activeListOderTraderBuy == true) }" v-if="activeListOderTraderBuy == true || activeListOderTrader == true">
              <TradeOrderListBuy :activeListOderTrader="activeListOderTrader" :activeListOderTraderBuy="activeListOderTraderBuy"/>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { mapGetters, mapActions } from 'vuex';
  /* js */
  import MYTronWeb from './../../js/tron-web.js';
  /* component */
  import TradeOrderListBuy from './Trade-Order-List-Buy.vue';
  
  export default {
    components: {
        TradeOrderListBuy
    },
    data () {
      return {
          pair: '',
          traderAddr: '',
          activeListOderTraderBuy  :false,
          activeListOderTraderSell : false,
          activeListOderTrader : true,
          selected: 6
      }
    },
    mounted () {
      let self = this;
      this.traderAddr = MYTronWeb.getAccount();
      this.pair = this.$route.params.id;
      this.onPairChange();
      this.interval =    setInterval(() => {
        if (MYTronWeb.getAccount() != self.traderAddr) {
          self.traderAddr = MYTronWeb.getAccount();
        }
      }, 1000);
    },
    destroyed() {
       clearInterval(this.interval);
    },
    computed: {
      ...mapGetters([
        'tradeListToken',
        'tradeIdActive',
        'tradeOrderSellList',
        'tradeOrderBuyList',
        'tradeSortByDecimal',
        'tradeLastHistory',
        'priceLastTrade',
        'tradeToken',
        'priceUsdOnTrx',
        'checkMarketToken'
      ])
    },
    methods: {
      /** ---------------------------------------------------------------------------------------------------------
      * Action Map By Store
      *  ----------------------------------------------------------------------------------------------------------
      */
      ...mapActions([
          'setTradeOrderSellActive',
          'setTradeSortByDecimal',
          'setTradePriceToken'
      ]),

      /*--------------------------- ON CHANGE STORE ----------------------------*/

      onPairChange() {
        let self = this;
        this.$store.watch(
          (state)=>{
            return this.$store.getters.tradeIdActive;
          },
          (val)=>{
            //something changed do something
            this.pair = val;
          },
          {
            deep:true
          }
        ); 
      },

      /*----------------------- VALIDATE , CHECK , EVENT -----------------------*/

      numberWithCommas(x, decimals = 3) {
        if (isNaN(x) == true) x = 0;
        x = parseFloat(x).toFixed(decimals);
        x  = x.split(".");
        x[0] = x[0].toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        x = x.join('.');
        return x;
      },
      isMarketDown() {
        if (!this.checkMarketToken) return false;
        if (this.checkMarketToken < 0) return true;
        return false;
      },
      isMarketUp() {
        if (!this.checkMarketToken) return false;
        if (this.checkMarketToken > 0) return true;
        return false;
      },
      isYourOrder(orderDetail) {
        let traderAddrList = orderDetail.traderAddrList;
        return traderAddrList.includes(this.traderAddr);
      },
      isShowOrderSellDetail(orderDetail, index) {
        if (this.tradeOrderSellList[this.pair] && orderDetail.isActive == true) {
          let totalOrder = this.tradeOrderSellList[this.pair].length;
          if (this.activeListOderTrader == true && totalOrder > 12 && index < totalOrder - 12) {
            return false;
          } 
          if (this.activeListOderTrader == false && this.activeListOderTraderSell == true && orderDetail.isDefault == true) {
            return false;
          }
          return true;
        }
        return false;
      },
      showListTraderBuy(){
        let self = this;
        self.activeListOderTraderSell = false;
        self.activeListOderTraderBuy = true;
        self.activeListOderTrader  = false;
      },
      showListTraderSell(){
        let self = this;
        self.activeListOderTraderSell = true;
        self.activeListOderTraderBuy = false;
        self.activeListOderTrader  = false;
      },
      showListTrader(){
        let self = this;
        self.activeListOderTrader = true;
        self.activeListOderTraderSell = false;
        self.activeListOderTraderBuy = false;
      },
      handleChange(e) {
        if(e.target.options.selectedIndex > -1) {
            var num = parseInt(e.target.options[e.target.options.selectedIndex].dataset.foo);
            this.setTradeSortByDecimal(num);
        }
      },
      displayFloatValue(value, num) {
        return parseFloat(value).toFixed(num); 
      },
      displayFloatValueMarket(value, num) {
        if(isNaN(value)){
          value = 0;
        }
        return parseFloat(value).toFixed(num); 
      },
    }  
  }
</script>
<style lang="scss" scoped>
    .up{
      color:green;
    }
    .down{
      color : red;
    }
</style>
