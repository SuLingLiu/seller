<template>
	<div class="goods">
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li class="menu-item" v-for="item in goods">
          <span class="text">
            <span class="icon" v-show="item.type>=0" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div> 

    <div class="foods-wrapper" v-el:food-wrapper>
      <ul>
        <li class="food-list food-list-hook" v-for="item in goods">
          <h2 class="title">{{item.name}}</h2>
            <ul class="food-item-wrap">
              <li class="food-item" v-for="food in item.foods">
                <div class="icon">
                  <img :src="food.icon">
                </div>
                <div class="content">
                  <h3 class="name">{{food.name}}</h3>
                  <p v-show="food.description" class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount || 0}}份</span><span>好评率{{food.rating || 0}}%</span>
                  </div>

                  <div class="price">
                    <span class="now">￥{{food.price}}</span>
                    <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                  </div>
                </div>
              </li>
            </ul>
        </li>
      </ul>
    </div>

    <shopcart></shopcart>
  </div>
</template>

<script type="text-ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart';

  const ERR_OK = 0;
  export default {
    data () {
      return {
        goods: [],
        listHeight: []//用来存放右侧列表的区间高
      };
    },
    created () {
      this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
      this.$http.get('/api/goods').then(response => {
      // success callback
        response = response.body;
        if(response.errno === ERR_OK) {
          this.goods = response.data;
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
          })
        }
      }, response => {
        // error callback
        console.log('goods--error');
      });
    },
    methods: {
      _initScroll () {
        this.meunScroll = new BScroll(this.$els.menuWrapper,{});
        this.foodsScroll = new BScroll(this.$els.foodWrapper,{});
      },
      _calculateHeight () {
        //food-list-hook这里添加的class只用来操作dom
        let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');

        let height = 0;
        this.listHeight.push(height);
        for(var i=0; i<foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }

      }
    },
    components: {
      shopcart
    }
  };
</script>
  
<style lang="scss">
  @import "../../common/scss/mixin.scss";
	.goods {
    position: absolute;
    @include px2rem('top', 352px);
    @include px2rem('bottom', 96px);
    display: flex;
    width: 100%;
    overflow: hidden;

    // == 左边菜单
    .menu-wrapper {
      @include px2rem('width', 160px);
      background: #f3f5f7;
      .menu-item {
        @include px2rem('padding', 0px 24px);
        @include px2rem('height', 108px);
        display: table;
        color: rgb(7,85,93);

        &.active {
          background: #fff;
          color: rgb(240,20,20);
        }
        .text {
          display: table-cell;
          vertical-align: middle;
          border-bottom: 1px solid rgba(7,17,27,0.1);
          
          @include px2rem('line-height', 28px);
          @include font-dpr(12px);
          .icon {
            display: inline-block;
            vertical-align: top;
            @include px2rem('width', 24px);
            @include px2rem('height', 24px);
            @include px2rem('margin-right', 4px);
            background-size: cover;
            background-repeat: no-repeat;

            &.decrease{
              @include bg-image('decrease_3');
            }
            &.discount{
              @include bg-image('discount_3');
            }
            &.guarantee{
              @include bg-image('guarantee_3');
            }
            &.invoice{
              @include bg-image('invoice_3');
            }
            &.special{
              @include bg-image('special_3');
            }
          }
        }

        &:last-child .text {
          border: none;
        }
      }
    }


    // == 右边食物
    .foods-wrapper {
      width: 1;
      flex: 1;

      .food-list {
        .title {
          @include px2rem('height', 52px);
          @include px2rem('line-height', 52px);
          @include px2rem('padding-left', 26px);
          color: rgb(147,153,159);
          @include font-dpr(12px);
          border-left: 2px solid #d9dde1;
          background: #f3f5f7;
        }
        .food-item-wrap {
          @include px2rem('padding', 0px 36px);
        }
        .food-item {
          display: flex;
          @include px2rem('padding', 36px 0px);
          border-bottom: 1px solid rgba(7,17,17,0.1);
          &:last-child {
            border: none;
          }
          .icon {
            @include px2rem('width', 114px);
            @include px2rem('height', 114px);
            img {
              width: 100%;
              height: 100%;
            }
          }

          .content {
            width: 1;
            flex: 1;
            @include px2rem('padding-left', 20px);
            .name {
              @include px2rem('padding', 4px 0px 16px);
              @include px2rem('line-height', 28px);
              @include font-dpr(14px);
              color: rgb(7,17,27);
            }
            .desc,.extra {
              @include px2rem('padding-bottom', 16px);
              @include px2rem('line-height', 20px);
              @include font-dpr(10px);
              color: rgb(147,153,159);
            }
            .extra {
              .count {
                @include px2rem('padding-right', 24px);
              }
            }

            .price {
              font-weight: 700;
              @include px2rem('line-height', 28px);

              .now {
                @include font-dpr (14px);
                @include px2rem('margin-right', 16px);
                color: rgb(240,20,20);
              }

              .old {
                text-decoration: line-through;
                @include font-dpr (10px);
                color: rgb(147,153,159);
              }
            }
          }
        }
      }
    }
  }
</style>