<template>
	<div class="cartcontrol">
		<div class="cart cart-decrease" v-show="food.count>0" @click.stop.prevent="decreaseCart($event)" transition="move">
			<div class="inner icon-remove_circle_outline"></div>
		</div>
		<div class="cart cart-count" v-show="food.count>0">{{food.count}}</div>
		<div class="cart cart-add" @click.stop.prevent="addCart($event)">
			<div class="inner icon-add_circle"></div>
		</div>
	</div>
</template>

<script type="text-ecmascript-6">
	import Vue from 'vue';

  export default {
  	props: {
  		food: {
  			type:Object
  		}
  	},
    data () {
      return {};
    },
    created() {
    },
    methods: {
    	addCart(event) {
    		if(!event._constructed) {//这个属性，浏览器原生的是没有这个属性的
	         	return false;
	        } 
    		if(!this.food.count) {
    			//this.food.count = 1;//直接添加一个data里不存在的属性时，是不能被观测到
    			//子组件的数据发生变化，父组件的数据也会发生变化
    			Vue.set(this.food,'count', 1);
    		}else {
    			this.food.count ++;
    		}

    		//派发事件
    		this.$dispatch('cart.add',event.target);
    	},
    	decreaseCart() {
    		if(!event._constructed) {//这个属性，浏览器原生的是没有这个属性的
	         	return false;
	        }
	        if(this.food.count) {
    			this.food.count --;
    		}
    	}
    }
  }
</script>

<style lang="scss">
	@import "../../common/scss/mixin.scss";
	.cartcontrol {
		font-size: 0;
		overflow: hidden;
		.cart {
			display: inline-block;
			vertical-align: top;
			color: rgb(0,166,220);
			@include px2rem(line-height, 48px);
		}
		.cart-decrease, .cart-add {
			@include px2rem(padding, 8px);
			@include px2rem(font-size, 48px);

		}
		.cart-decrease {
			transition: 0.4s;
			&.move-transition {
				opacity: 1;
				transform: translate3d(0,0,0);
				.inner {
					transition: 0.4s;
					display: inline-block;
					transform: rotate(0);
				}
			}
			&.move-enter,&.move-leave {
				opacity: 0;
				transform: translate3d(24px,0,0);
				.inner {
					transform: rotate(180deg);
				}
			}
			
		}
		.cart-count {

			@include px2rem(width, 32px);
			@include px2rem(height, 48px);
			@include px2rem(margin-top, 8px);
			@include px2rem(font-size, 20px);
			color: rgb(147,153,159);
			text-align: center;
		}
	}
</style>