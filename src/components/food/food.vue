<template>
  <div class="food" v-show="showFlag" transition="move">
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
      </div>
    </div>
  </div>
</template>

<script type="text-ecmascript-6">
  import BScroll from 'better-scroll';
  export default{
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false
      }
    },
    methods: {
      //父组件调用子组件的方法，并改变子组件的data值
      show() {
        this.showFlag = true;
      },
      hide() {
        this.showFlag = false;
      }
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
  }
</style>