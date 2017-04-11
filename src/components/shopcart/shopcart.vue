<template>
	<div class="shopcart">
		<div class="content">
			<div class="content-left">
				<div class="logo-wrapper">
					<div class="logo">
						<span class="icon-shopping_cart"></span>
					</div>
					<span class="num">{{totalCount}}</span>
				</div>
				<div class="price">￥{{totalPrice}}</div>
				<div class="desc">另需配送费￥{{deliveryPrice}}元</div>
			</div>
			<div class="content-right"><span class="pay">￥{{minPrice}}元起送</span></div>
		</div>
	</div>
</template>

<script type="text-ecmascript-6">

  export default {
  	props: {
  		selectFoods: {//已选择的商品
  			type: Array,
  			default () {
  				return [
  					{
  						price: 10,
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
      return {};
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
    	}
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
				}
				.desc {
					@include px2rem(padding-left, 24px);
				}
			}
			.content-right {
				@include px2rem(width, 210px);
				background: #2b333b;
				text-align: center;
				.pay {
					@extend %shop-text;
				}
			}
		}
	}
</style>