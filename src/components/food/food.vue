<template>
  <div class="food" v-show="showFlag" transition="move" v-el:food>
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image">
        <div class="back">
          <i class="icon-arrow_lift" @click="hide"></i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="deail">
          <span class="sell-count">月售{{food.sellCount}}份</span>
          <span class="rating">好评率{{food.rating}}</span>
        </div>
        <div class="price">
          <span class="now">￥{{food.price}}</span>
          <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
        </div>


        <div class="cartcontrol-wraaper">
          <cartcontrol :food="food"></cartcontrol>
        </div>
        <div class="buy" @click.stop.prevent="addFist($event)" v-show="!food.count || food.count==0" transition="fade">立即购买</div>
      </div>
      <split v-show="food.info"></split>
      <div class="info" v-show="food.info">
        <h1 class="title">商品信息</h1>
        <div class="text">{{ food.info}}</div>
      </div>
      <split></split>
      <div class="rating">
        <h1 class="title">商品评价</h1>
        <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
        <div class="rating-wrapper">
          <ul v-show="food.ratings && food.ratings.length">
            <li v-show="needShow(rating.rateType,rating.text)" class="rating-item" v-for="rating in food.ratings">
              <div class="user">
                <span class="name">{{rating.username}}</span>
                <img :src="rating.avatar" class="avatar">
              </div>
              <div class="time">{{rating.rateTime}}</div>
              <p class="text">
                <span :class="{'icon-thumb_up': rating.rateType===0,'icon-thumb_down': rating.rateType ===1}"></span>{{rating.text}}
              </p>
            </li>
          </ul>
          <div v-show="!food.ratings || !food.ratings.length"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="text-ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import split from 'components/split/split';
  import ratingselect from 'components/ratingselect/ratingselect';
  import Vue from 'vue';
  // const POSITIVE = 0;
  // const NEGATIVE = 1;
  const ALL = 2;
  export default{
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      //父组件调用子组件的方法，并改变子组件的data值
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = false;
        this.$nextTick(() => {
          if(!this.scroll) {
            this.scroll = new BScroll(this.$els.food,{
              click: true
            })
          }else {
            this.scroll.refresh();
          }
        })
      },
      hide() {
        this.showFlag = false;
      },
      addFist(event) {
        if(!event._constructed) {//这个属性，浏览器原生的是没有这个属性的
          return false;
        } 
        //派发事件
        this.$dispatch('cart.add',event.target);//抛小球的动画计算时该元素display:none，获取不准，要解决这个问题可以给这个buy加动画
        Vue.set(this.food,'count',1);
      },
      needShow(type,text) {
        if(this.onlyContent && !text) {
          return false;
        }
        if(this.selectType === ALL) {
          return true;
        }else {
          return type === this.selectType;
        }
      }
    },
    events: {
      'ratingtype.select'(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      'content.toggle'(onlyContent) {
        this.onlyContent = onlyContent;
        //更新这里是需要dom先更新
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  };
</script>

<style lang="scss">
  @import "../../common/scss/mixin.scss";
  .food {
    position: fixed;
    left:0;
    @include px2rem(bottom, 96px);
    top: 0;
    z-index: 30;
    width: 100%;
    background: #fff;
    overflow: hidden;
    &.move-transition {
      transition: 0.2s linear;
      transform: translate3d(0,0,0);
    }
    &.move-enter, &.move-leave {
      transform: translate3d(100%,0,0);
    }
    
    //以下的这个处理，是因为图片加载时异步的的，高度不能写死，根据宽度来变化，如果不做处理会抖动，padding-top:100%是相对于width来的，
    .image-header {
      position: relative;
      width: 100%;
      height: 0;
      padding-top: 100%;
      img {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
      }
      .back {
        position: absolute;
        left: 0;
        top: 0;
        .icon-arrow_lift {
          display: block;
          @include px2rem(padding, 20px);
          color: #fff;
          @include px2rem(font-size, 40px);
        }
      }
    }
    .content {
      position: relative;
      @include px2rem(padding, 36px);
      .title {
        @include px2rem(line-height, 28px);
        @include px2rem(margin-bottom, 16px);
        @include px2rem(font-size, 24px);
        font-weight: 700;
        color: rgba(7,17,27,1);
      }
      .detail {
        @include px2rem(line-height, 20px);
        @include px2rem(margin-bottom, 36px);
        font-size: 0px;
        color: rgb(147,153,159);
        .sell-count, .rating {
          @include px2rem(font-size, 20px);
        }
        .sell-count {
          @include px2rem(margin-right, 24px);
        }
      }
      .price {
        @include px2rem(line-height, 48px);
        font-size: 0px;
        .now {
          @include px2rem(font-size, 20px);
          color: rgb(240,20,20);
          @include px2rem(margin-right, 24px);
          font-weight: 700;
        }
        .old {
          @include px2rem(font-size, 20px);
          color: rgb(147,153,159);
          font-weight: 700;
          text-decoration: line-through;
        }
      }
    }

    .cartcontrol-wraaper {
      position: absolute;
      @include px2rem(bottom, 24px);
      @include px2rem(right, 24px);
    }
    .buy {
      position: absolute;
      @include px2rem(bottom, 34px);
      @include px2rem(right, 36px);
      z-index: 10;
      @include px2rem(height, 48px);
      @include px2rem(line-height, 48px);
      @include px2rem(padding, 0px 24px);
      @include px2rem(font-size, 20px);
      box-sizing: border-box;
      color: #fff;
      border-radius: 12px;
      background: rgb(0,160,220);
      &.fade-transition {
        transition: 0.2;
        opacity: 1;
      }
      &.enter-transition,&.enter-leave {
        opacity: 0;
      }
    }

    .info {
      @include px2rem(padding, 36px);
      @include px2rem(font-size, 24px);
      .title {
        @include px2rem(line-height, 24px);
        @include px2rem(font-size, 28px);
        @include px2rem(margin-bottom, 12px);
        color: rgb(7,17,27);
      }
      .text {
        @include px2rem(line-height, 48px);
        @include px2rem(font-size, 24px);
        @include px2rem(text-indent, 24px);
        color: rgb(77,85,93);
      }
    }

    .rating {
      @include px2rem(padding-top, 36px);
      .title {
        @include px2rem(padding-left, 36px);
        @include px2rem(line-height, 28px);
        @include px2rem(font-size, 28px);
        color: rgb(7,17,27);
      }

      .rating-wrapper {
        @include px2rem(padding, 0px 36px);
        .rating-item {
          position: relative;
          @include px2rem(padding, 36px 0px);
          border-bottom: 1px solid rgba(7,17,27,0.2);
          .user {
            position: absolute;
            right: 0;
            @include px2rem(top, 32px);
            @include px2rem(font-size, 24px);
            @include px2rem(line-height, 24px);
            .name {
              display: inline-block;
              vertical-align: top;
              @include px2rem(font-size, 20px);
              @include px2rem(line-height, 24px);
              @include px2rem(margin-right, 12px);
              color: rgb(147,153,159);
            }
            .avatar {
              border-radius: 50%;
              @include px2rem(width, 24px);
              @include px2rem(height, 24px);
            }
          }
          .time {
            @include px2rem(margin-bottom, 12px);
            @include px2rem(line-height, 24px);
            @include px2rem(font-zize, 20px);
            color: rgb(147,153,159);
          }
          .text {
            @include px2rem(line-height, 32px);
            @include px2rem(font-zize, 24px);
            color: rgb(7,17,27);
            .icon-thumb_up, .icon-thumb_down {
              @include px2rem(margin-right, 8px);
            }
            .icon-thumb_up {
              color: rgb(0,160,220);
            }
            .icon-thumb_down {
              color: rgb(147,153,159);
            }
          }
        }
      }
    }
  }
</style>