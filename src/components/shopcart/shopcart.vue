<template>
	<div class="shopcart">
		<div class="content" @click="toggleList">
			<div class="content-left">
				<div class="logo-wrapper">
					<div class="logo" :class="{'highlight':totalCount>0}">
						<span class="icon-shopping_cart"></span>
					</div>
					<span class="num" v-show="totalCount>0">{{totalCount}}</span>
				</div>
				<div class="price" :class="{'highlight':totalCount>0}">￥{{totalPrice}}</div>
				<div class="desc">另需配送费￥{{deliveryPrice}}元</div>
			</div>
			<div class="content-right" :class="payClass" @click.stop.prevent="pay"><span class="pay">{{payDesc}}</span></div>
		</div>
		<div class="ball-container">
			<div v-for="ball in balls" transition="drop" class="ball" v-show="ball.show">
				<div class="inner inner-hook"></div>
			</div>
		</div>
		<div class="shopcart-list" v-show="listShow" transition="fold">
			<div class="list-header">
				<h1 class="title">购物车</h1>
				<span class="empty" @click="empty">清空</span>
			</div>
			<div class="list-content" v-el:list-content>
				<ul>
					<li class="food" v-for="food in selectFoods">
						<span class="name">{{food.name}}</span>
						<div class="price">
							<span>￥{{food.price*food.count}}</span>
						</div>
						<div class="cartcontrol-wrapper">
							<cartcontrol :food="food"></cartcontrol>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
	<div class="list-mask" v-show="listShow" @click="hideList" transition="fade"></div>
</template>

<script type="text-ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  export default {
  	props: {
  		selectFoods: {//已选择的商品
  			type: Array,
  			default () {
  				return [
  					{
  						price: 20,
  						count: 1
  					}
  				];
  			}
  		},
  		deliveryPrice: {
  			type: Number
  		},
  		minPrice: {
  			type: Number
  		} 
  	},
    data () {
      return {
      	balls: [
	      	{
	      		show: false
	      	},
	      	{
	      		show: false
	      	},
	      	{
	      		show: false
	      	},
	      	{
	      		show: false
	      	},
	      	{
	      		show: false
	      	}
      	],
      	dropBalls: [],//已经下落的小球
      	fold: true //下面的购物车面板折叠
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
    	payDesc() {
    		if(this.totalPrice === 0) {
    			return `￥${this.minPrice}元起送`;
    		}else if(this.totalPrice<this.minPrice) {
    			let diff = this.minPrice - this.totalPrice;
    			return `还差￥${diff}元`;
    		}else {
    			return '去结算';
    		}
    	},
    	payClass() {
    		if(this.totalPrice<this.minPrice) {
    			return 'not-enough';
    		}else {
    			return 'enough';
    		}
    	},
    	listShow() {
    		if(!this.totalCount) {
    			this.fold = true;
    			return false;
    		}

    		let show = !this.fold;
    		if(show) {
    			this.$nextTick(() => {
    				if(!this.scroll) {
	    				this.scroll = new BScroll(this.$els.listContent,{
	    					click: true
	    				})
    				}else {
    					this.scroll.refresh();
    				}
    			})
    		}
    		return show;
    	}
    },
    methods: {
    	drop(el) {
    		for(let i=0; i<this.balls.length; i++) {
    			let ball = this.balls[i];
    			if(!ball.show) {
    				ball.show = true;
    				ball.el = el;
    				this.dropBalls.push(ball);
    				return;
    			}
    		}
    	},
    	toggleList() {
    		if(!this.totalCount) {
    			return;
    		}
    		this.fold = !this.fold;
    	},
    	empty() {
    		this.selectFoods.forEach((food) => {
    			food.count = 0;
    		})
    	},
    	hideList() {
    		this.fold = true;
    	},
    	pay() {
    		//@click.stop.prevent 阻止冒泡
    		if(this.totalPrice<this.minPrice) {
    			return;
    		}

    		alert('支付');


    	}
    },
    transitions: {
    	drop: {
    		beforeEnter(el) {
    			let count = this.balls.length;
    			while (count --) {
    				let ball = this.balls[count];
    				if(ball.show) {
    					let rect = ball.el.getBoundingClientRect();
    					let x = rect.left -32;
    					let y = -(window.innerHeight - rect.top -22);
    					el.style.display = '';
    					el.style.webkitTransform = `translate3d(0,${y}px,0)`;
    					el.style.transform = `translate3d(0,${y}px,0)`;
    					let inner = el.getElementsByClassName('inner-hook')[0];
    					inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
    					inner.style.transform = `translate3d(${x}px,0,0)`;
    				}
    			}
    		},
    		enter(el) {
    			/* eslint-disable no-unused-vars */
    			let rf = el.offsetHeight;//主动触发浏览器重绘,rf变量没用到，es6会保错，所以有加注释
    			this.$nextTick(() => {
    				el.style.webkitTransform = 'translate3d(0,0,0)';
					el.style.transform = 'translate3d(0,0,0)';
					let inner = el.getElementsByClassName('inner-hook')[0];
					inner.style.webkitTransform = 'translate3d(0,0,0)';
					inner.style.transform = 'translate3d(0,0,0)';
    			})
    		},
    		afterEnter(el) {
    			let ball = this.dropBalls.shift();
    			if(ball) {
    				ball.show = false;
    				el.style.display = 'none';
    			}
    		}
    	}
    },
    components: {
      cartcontrol
    }

  }
</script>

<style lang="scss">
	@import "../../common/scss/mixin.scss";
	%shop-text {
		display: inline-block;
		vertical-align: top;
		@include px2rem(height, 48px);
		@include px2rem(margin-top, 24px);
		@include px2rem(font-size, 32px);
	}
	.shopcart {
		position: fixed;
		left: 0;
		bottom: 0;
		z-index: 1000;
		width: 100%;
		@include px2rem(height, 96px);
		@include px2rem(line-height, 48px);
		background: #141d27;
		color: rgba(255,255,255,0.4);
 
		.content {
			display: flex;
			position: relative;
			z-index: 2;
			background: #141d27;
			.content-left {
				flex: 1;
				font-size: 0;
				.logo-wrapper {
					display: inline-block;
					position: relative;
					@include px2rem(top, -20px);
					@include px2rem(width, 88px);
					@include px2rem(height, 88px);
					@include px2rem(margin, 0px 24px);
					@include px2rem(line-height, 88px);
					@include px2rem(padding, 12px);
					background: #131d26;
					border-radius: 50%;

					.logo {
						width: 100%;
						height: 100%;
						border-radius: 50%;
						background: #2b343c;
						@include px2rem(font-size, 48px);
						text-align: center;
						&.highlight {
							background: rgb(0,160,220);
							color: #fff;
						}
					}
					.num {
						position: absolute;
						top: 0;
						right: 0;
						@include px2rem(width, 48px);
						@include px2rem(height, 32px);
						background: rgb(240,20,20);
						@include px2rem(line-height, 32px);
						@include px2rem(font-size, 18px);
						color: #fff;
						font-weight: 700;
						text-align: center;
						@include px2rem(border-radius, 18px);
					}
				}
				.price, .desc {
					@extend %shop-text;
				}
				.price {
					@include px2rem(padding-right, 24px);
					border-right: 1px solid rgba(255,255,255,0.1);
					font-weight: 700;
					&.highlight {
						color: #fff;
					}
				}
				.desc {
					@include px2rem(padding-left, 24px);
				}
			}
			.content-right {
				@include px2rem(width, 210px);
				background: #2b333b;
				text-align: center;
				&.enough {
					background: #00a0dc;
					color: #fff;
				}
				.pay {
					@extend %shop-text;
				}
			}
		}
		.ball-container {

			.ball {
				position: fixed;
				@include px2rem(left, 64px);
				@include px2rem(bottom, 44px);
				z-index: 100;
				&.drop-transition {
					transition: 0.4s cubic-bezier(.55,-0.44,.83,.67);
				}
				.inner {
					@include px2rem(width, 32px);
					@include px2rem(height, 32px);
					border-radius: 50%;
					background: rgb(0,160,220);
					transition: 0.4s linear;
				}
			}

		}
		.shopcart-list {
			position: absolute;
			left: 0;
			top: 0;
			z-index: -1;
			width: 100%;
			background: #fff;
			&.fold-transition {
				transition: 0.5s;
				transform: translate3d(0,-100%,0);
			}
			&.fold-enter, &.fold-leave {
				transform: translate3d(0,0,0)
			}
			.list-header {
				@include px2rem(height, 80px);
				@include px2rem(line-height, 80px);
				@include px2rem(padding, 0px 36px);
				background: #f3f5f7;
				border-bottom: 1px solid rgba(7,17,27,0.1);

				.title {
					float: left;
					@include px2rem(font-size, 28px);
					color: rgb(7,17,27);
				}
				.empty {
					float: right;
					@include px2rem(font-size, 24px);
					color: rgb(0,160,220);
				}
			}
			.list-content {
				overflow: hidden;
				@include px2rem(max-height, 434px);
				@include px2rem(padding, 0px 36px);
				background: #fff;
				
				.food {
					position: relative;
					box-sizing: border-box;
					@include px2rem(padding, 24px 0px);
					@include px2rem(line-height, 48px);
					border-bottom: 1px solid rgba(7,17,27,0.1);
					.name {
						color: rgb(7,17,27);
						@include px2rem(font-size, 28px);
					}
					.price {
						position: absolute;
						@include px2rem(bottom, 24px);
						@include px2rem(right, 180px);
						color: rgb(240,20,20);
						@include px2rem(font-size, 28px);
						font-weight: 700;
					}
					.cartcontrol-wrapper {
						position: absolute;
						@include px2rem(bottom, 12px);
						@include px2rem(right, 0px);
						color: rgb(0,160,220);
						@include px2rem(font-size, 48px);
					}
				}
			}
		}
	}
	.list-mask {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: 40;
		backdrop-filter:blur(10px);
		&.fade-transition {
			transition: 0.5s;
			opacity: 1;
			background: rgba(7,17,27,0.6);
		}
		&.fade-enter, &.fade-leave {
			opacity: 0;
			background: rgba(7,17,27,0);
		}
	}
</style>