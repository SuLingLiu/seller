<template>
	<div class="goods">
    <div class="menu-wrapper" v-el:menu-wrapper>
      <ul>
        <li class="menu-item" :class="{'current':currentIndex == $index}" v-for="item in goods" @click="selectMenu($index,$event)">
          <span class="text">
            <span class="icon" v-show="item.type>=0" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div> 

    <div class="foods-wrapper" v-el:food-wrapper>
      <ul>
        <li class="food-list food-list-hook" v-for="item in goods" >
          <h2 class="title">{{item.name}}</h2>
            <ul class="food-item-wrap">
              <li class="food-item" v-for="food in item.foods" @click="selectFood(food,$event)">
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
                  <div class="cartcontrol-wrap">
                    <cartcontrol :food="food"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
        </li>
      </ul>
    </div>

    <shopcart v-ref:shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
  </div>
  <food :food="selectedFood" v-ref:food></food>
</template>

<script type="text-ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import food from 'components/food/food';

  const ERR_OK = 0;
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],//用来存放右侧列表的区间高
        scrollY: 0,
        selectedFood: {}
      };
    },
    computed: {
      currentIndex() {
        for(let i=0; i<this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i+1];
          if(height2 && (this.scrollY>=height1 && this.scrollY < height2)) {
            return i;
          }
        }

        return 0;
      },
      selectFoods() {
        let foods = [];
        //这里的this.goods发生变化，他就会重新被计算
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if(food.count) {
              foods.push(food);
            }
          })
        })
        return foods;
      }
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
      _drop(target) {
        //调用子组件的方法 v-ref:shopcart,写到nextTick里是为了让抛小球的效果，晚点触发，这样减得效果，和抛小球的效果就不会那么卡
        //体验优化，异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        })

      },
      _initScroll () {
        this.meunScroll = new BScroll(this.$els.menuWrapper,{
          click: true//让滚动区的元素可以点击
        });
        this.foodsScroll = new BScroll(this.$els.foodWrapper,{
          probeType: 3,//添加这个属性，是要告诉我们滚动条实时的位置
          click: true
        });

        this.foodsScroll.on('scroll',(pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        })
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
      },
      selectMenu(index,event) {
        //在PC端点击会触发两次
        if(!event._constructed) {//这个属性，浏览器原生的是没有这个属性的
          return false;
        } 
        let foodList = this.$els.foodWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el,300);
      },
      selectFood(food,event) {
        if(!event._constructed) {//这个属性，浏览器原生的是没有这个属性的
          return false;
        } 
        this.selectedFood = food;
        this.$refs.food.show();
      }
    },
    events: {
      'cart.add'(target) {
        this._drop(target);
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
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
        width: 100%;
        box-sizing: border-box;
        text-align: center;
        &.current {
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
            position: relative;
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
            .cartcontrol-wrap {
              position: absolute;
              right: 0;
              @include px2rem('bottom', 0px);
            }
          }
        }
      }
    }
  }
</style>