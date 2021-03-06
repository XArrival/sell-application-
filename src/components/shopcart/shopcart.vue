<template>
  <div class="shopcart">
    <div class="content" @click="toggleShow">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight': totalCount > 0}">
            <i class="icon-shopping_cart" :class="{'highlight': totalCount > 0}"></i>
          </div>
          <div v-show="totalCount>0" class="num">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalPrice > 0}">￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click.stop.prevent="pay">
        <div class="pay" :class=payClass>
          {{payDesc}}
        </div>
      </div>
    </div>
    <div class="shopcart-list" v-show="showList" transition="fold">
      <div class="header">
        <h1 class="text">购物车</h1>
        <h1 class="clear" @click="clear">清空</h1>
      </div>
      <div class="shop-list" v-el:shop-list>
        <ul>
          <li class="list" v-for="food in selectFoods">
            <h1 class="list-name">{{food.name}}</h1>
            <span class="price">￥{{food.price * food.count}}</span>
            <cartcontrol class="cartcontrol" :food="food"></cartcontrol>
          </li>
        </ul>
      </div>
    </div>
    <div class="shop-mask" @click="hide" v-show="showList" transition="fade"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  import BScroll from 'better-scroll';

  export default {
    props: {
      selectFoods: {
        type: Array,
        default() {
          return [
            {
              price: 0,
              count: 0
            }
          ];
        }
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        fold: true
      };
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      showList() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        var show = !this.fold;
        if (show) {
          if (!this.shopScroll) {
             console.log('初始化');
             this.$nextTick(() => {
                 this.shopScroll = new BScroll(this.$els.shopList, {
                   click: true
               });
             });
          } else {
             console.log('refresh');
             this.shopScroll.refresh();
          }
        }
        return show;
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return '￥' + this.minPrice + '元起送';
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return '还差￥' + diff + '元起送';
        } else {
          return '去结算';
        }
      },
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      }
    },
    methods: {
      toggleShow() {
         if (!this.totalCount) {
           return;
         }
         console.log(this.fold);
         this.fold = !this.fold;
      },
      hide() {
        this.fold = true;
      },
      pay() {
        if (this.totalPrice < 20) {
            console.log(`还差${20 - this.totalPrice}元起送`);
            window.alert(`还差${20 - this.totalPrice}元起送`);
        } else {
            console.log(`需要支付${this.totalPrice}元`);
            window.alert(`需要支付${this.totalPrice}元`);
        }
      },
      clear() {
        this.selectFoods.forEach((food) => {
           console.log('清空购物车');
           food.count = 0;
        });
      }
    },
    components: {
      cartcontrol,
      BScroll
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .shopcart
    position: fixed
    left: 0
    bottom: 0
    z-index: 50
    width: 100%
    height: 48px
    .content
      display: flex
      background: #141d27
      font-size: 0
      color: rgba(255, 255, 255, 0.4)
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          vertical-align: top
          position: relative
          top: -10px
          margin: 0 12px
          padding: 6px
          width: 56px
          height: 56px
          box-sizing: border-box
          border-radius: 50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius: 50%
            background: #2b343c
            text-align: center
            &.highlight
              background: rgb(0, 160, 220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.highlight
                color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            color: #fff
            background: rgb(240, 20, 20)
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display: inline-block
          vertical-align: top
          line-height: 24px
          margin-top: 12px
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          font-weight: 700
          &.highlight
            color: #fff
        .desc
          display: inline-block
          vertical-align: top
          margin: 12px 0 0 12px
          font-size: 10px
          line-height: 24px
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          /*水平居中*/
          font-size: 12px
          font-weight: 700
          &.not-enough
            background: #2b333b
          &.enough
            background: #00b43c
            color: #fff
    .shopcart-list
      position: absolute
      left: 0
      top: 0
      z-index: -1
      width: 100%
      &.fold-transition
        transition: all 0.5s
        transform: translate3d(0, -100%, 0)
      &.fold-enter,&.fold-leave
        transform: translate3d(0, 0, 0)
      .header
        padding: 0 18px
        height: 40px
        line-height: 40px
        background-color: #f3f5f7
        border-bottom: 2px solid rgba(7, 17, 27, 0.1)
        .text
          float: left
          font-size: 14px
          font-weight: 200
          color: rgb(7, 17, 27)
        .clear
          float: right
          font-size: 12px
          color: rgb(0, 160, 220)
      .shop-list
        padding: 0 18px
        background: #fff
        max-height: 186px
        overflow: hidden
        .list
          padding: 12px 0
          border-1px(rgba(7, 17, 27, 0.1))
          box-sizing:border-box
          .list-name
            font-size: 14px
            color: rgb(7, 17, 27)
            line-height: 24px
          .price
            position: absolute
            right: 90px
            bottom: 12px
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
            line-height: 24px
          .cartcontrol
            position: absolute
            right: 0
            bottom: 6px
    .shop-mask
      z-index: -2
      position: fixed
      top: 0
      left: 0
      width: 100%
      height: 100%
      &.fade-transition
        transition: all 0.5s
        opacity: 1
        background: rgba(7, 17, 27, 0.6)
      &.fade-enter, &.fade-leave
        opacity: 0
</style>
