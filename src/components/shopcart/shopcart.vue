<template>
	<div class="shopcart">
		<div class="content">
			<div class="content-left">
				<div class="logo-wrapper">
					<div class="logo">
						<span class="icon-shopping_cart"></span>
					</div>
				</div>
				<div class="price">￥0</div>
				<div class="desc">另需配送费￥4元</div>
			</div>
			<div class="content-right"><span>￥20元起送</span></div>
		</div>
	</div>
</template>

<script type="text-ecmascript-6">

  export default {
    data () {
      return {};
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
				span {
					@extend %shop-text;
				}
			}

		}
	}
</style>