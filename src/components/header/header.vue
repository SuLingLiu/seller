<template>
	<div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img :src="seller.avatar">
      </div>

      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>

      <div class="support-count" v-if="seller.supports"  @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span><i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    
    <!-- 公告 -->
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>

    <!-- 背景 -->
    <div class="background">
      <img :src="seller.avatar">
    </div>

    <!-- 详情弹窗 -->
    <div v-show="detailShow" class="detail clearfix" transition="fade">
      <div class="detail-wrapper">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>

          <div class="star-wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          
          <div class="title">
            <span class="line"></span>
            <span class="text">优惠信息</span>
            <span class="line"></span>
          </div>

          <ul class="supports" v-if="seller.supports">
            <li class="support-item" v-for="item in seller.supports">
                <span class="icon" :class="classMap[item.type]"></span>
                <span class="text">{{item.description}}</span>
            </li>
          </ul>

          <div class="title">
            <span class="line"></span>
            <span class="text">商家公告</span>
            <span class="line"></span>
          </div>

          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>

      <div class="detail-close" @click="hideDetail" >
        <i class="icon-close"></i>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
import star from 'components/star/star';
export default {
	props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      detailShow: false
    };
  },
  created() {
    this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
  },
  methods: {
    showDetail () {
      this.detailShow = true;
    },
    hideDetail () {
      this.detailShow = false;
    }
  },
  components: {
    star
  }
};
</script>

<style lang="scss" rel="stylesheet/scss">
  @import "../../common/scss/mixin.scss";
	.header {
    position: relative;
    background: rgba(7, 17, 27, 0.5);

    // == 内容
    .content-wrapper {
      position: relative;
      display: flex;
      @include px2rem('padding', 48px 24px 36px 48px);
      
      // == 头像
      .avatar {
        @include px2rem('width', 128px);
        @include px2rem('height', 128px); 

        img {
          width: 100%;
          height: 100%;
          @include px2rem('border-radius', 4px);
        }
      }
      
      // == 右侧内容
      .content {
        width: 1;
        flex: 1;
        color: rgb(255,255,255);

        @include px2rem('padding-left', 32px);
        .title {
          @include px2rem('margin', 4px 0px 16px);
          font-size: 0;
          span {
            display: inline-block;
            &.brand {
              vertical-align: top;
              @include px2rem('width', 60px);
              @include px2rem('height', 36px);
              @include bg-image('brand');
              background-size: cover;
            }

            &.name {
              @include font-dpr(16px);
              font-weight: bold;
              @include px2rem('line-height', 36px);
              @include px2rem('padding-left', 4px);
            }
          }
        }

        .description {
          @include font-dpr(12px);
          @include px2rem('line-height', 24px);
          @include px2rem('margin-bottom', 20px);
        }

        .support {
          .icon {
            display: inline-block;
            vertical-align: top;
            @include px2rem('width', 32px);
            @include px2rem('height', 32px);
            background-size: cover;
            background-repeat: no-repeat;

            &.decrease{
              @include bg-image('decrease_1');
            }
            &.discount{
              @include bg-image('discount_1');
            }
            &.guarantee{
              @include bg-image('guarantee_1');
            }
            &.invoice{
              @include bg-image('invoice_1');
            }
            &.special{
              @include bg-image('special_1');
            }
          }
          .text {
            display: inline-block;
            @include font-dpr(10px);
            @include px2rem('line-height', 24px);
            @include px2rem('padding-left', 8px);
          }
        }
      }

      // == 优惠数量
      .support-count {
        position: absolute;
        @include px2rem('bottom', 36px);
        @include px2rem('right', 24px);
        @include px2rem('padding', 14px 16px);
        @include px2rem('line-height', 24px);
        @include px2rem('border-radius', 24px);
        @include font-dpr(10px);
        background: rgba(0,0,0,0.2);
        text-align: center;

        color: rgb(255,255,255);
        .count {
          vertical-align: top;
          @include px2rem('padding-right', 4px); 
        }
      }
    } 
    
    // == 公告
    .bulletin-wrapper {
      position: relative;
      @include px2rem('height',56px);
      @include px2rem('line-height',56px);
      @include px2rem('padding',0px 44px 0px 24px);
      background:rgba(7,17,27,0.2);
      color: rgb(255,255,255);
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      @include font-dpr(10px);

      .bulletin-title {
        display: inline-block;
        vertical-align: top;
        @include px2rem('width',44px);
        @include px2rem('height',24px);
        @include px2rem('margin-top',16px);
        @include px2rem('margin-right',8px);
        @include bg-image('bulletin');
        background-size: cover;
        background-repeat: no-repeat;
      }
      .bulletin-text {
        vertical-align: top;
      }
      .icon-keyboard_arrow_right {
        position: absolute;
        @include px2rem('right',24px);
        @include px2rem('top',16px);
      }
    } 

    // == 背景
    .background {
      position: absolute;
      left: 0;
      top: 0;
      z-index: -1;
      width: 100%;
      height: 100%;
      filter: blur(10px);
      img {
        width: 100%;
        height: 100%;
      }
    }

    // == 详情弹窗
    .detail {
      position: fixed;
      left: 0;
      top: 0;
      z-index: 1001;
      width: 100%;
      height: 100%;
      overflow: auto;
      background: rgba(7, 17, 27, 0.8);
      transition: all 0.5s;
      backdrop-filter: blur(10px);
      &.fade-transition{
        opacity: 1;
        background: rgba(7,17,27,0.8);
      }
      &.fade-enter, &.fade-leave{
        opacity: 0;
        background: rgba(7,17,27,0);
      }

      .detail-wrapper {
        width: 100%;
        min-height: 100%;

        .detail-main {
          @include px2rem('padding',128px 72px);
          color: rgb(255,255,255);

          .name {
            @include font-dpr(12px);
            @include px2rem('line-height',32px);
            font-weight: 700;
            text-align: center;
          }

          .star-wrapper {
            text-align: center;
            @include px2rem('padding', 4px 0px);
            @include px2rem('margin', 32px 0px 56px 0px);
          }

          .title {
            display: flex;
            width: 100%;
            @include px2rem('margin-bottom', 48px);
            .line {
              position: relative;
              @include px2rem('top', -14px);
              flex: 1;
              width: 1;
              border-bottom: 1px solid rgba(255,255,255, 0.2)
            }
            .text {
              font-weight: 700;
              @include font-dpr(14px);
              @include px2rem('padding', 0px 24px);
              @include px2rem('line-height', 28px);
            }
          }

          .supports {
            @include px2rem('padding', 0px 24px);
            @include px2rem('margin-bottom', 48px);
            .support-item {
              font-size: 0;
              &:last-child {
                margin-bottom: 0;
              }
              @include px2rem('margin-bottom',24px);
              .icon {
                display: inline-block;
                vertical-align: top;
                @include px2rem('width', 32px);
                @include px2rem('height', 32px);
                background-size: cover;
                background-repeat: no-repeat;

                &.decrease{
                  @include bg-image('decrease_1');
                }
                &.discount{
                  @include bg-image('discount_1');
                }
                &.guarantee{
                  @include bg-image('guarantee_1');
                }
                &.invoice{
                  @include bg-image('invoice_1');
                }
                &.special{
                  @include bg-image('special_1');
                }
              }
              .text {
                display: inline-block;
                @include font-dpr(10px);
                @include px2rem('line-height', 24px);
                @include px2rem('padding-left', 12px);
              }
            }
          }
          
          .bulletin {
            @include px2rem('paddomg',48px 24px);
            .content {
              @include font-dpr(12px);
              @include px2rem('line-height',48px);
            }
          }

        }
      }

      .detail-close {
        position: relative;
        @include px2rem('width',64px);
        @include px2rem('height',64px);
        @include px2rem('margin-top',-128px);
        margin-left: auto;
        margin-right: auto;
        clear: both;
        @include font-dpr(32px);
        color: rgba(255,255,255,0.5);
      }
    }

  }
</style>