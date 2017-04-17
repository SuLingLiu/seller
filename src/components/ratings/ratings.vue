<template>
	<div class="ratings">
		<div class="ratings-content">
			<div class="overview">
				<div class="overview-left">
					<h1 class="score">{{seller.score}}</h1>
					<div class="title">综合评分</div>
					<div class="rank">高于周围商家{{seller.rankRate}}%</div>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
						<span class="title">服务态度</span>
						<star :size="36" :score="seller.serviceScore"></star>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-wrapper">
						<span class="title">商品评分</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="delivery-wrapper">
						<span class="title">送达时间</span>
						<span class="delivery">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<split></split>
			<ratingselect :select-type="selectType" :only-content="onlyContent" :ratings="ratings"></ratingselect>
			<div class="ratings-wrapper">
				<ul>
					<li class="rating-item" v-for="rating in ratings">
						<div class="avatar">
							<img :src="rating.avatar" alt="">
						</div>
						<div class="content">
							<h1 class="name">{{rating.name}}</h1>
							<div class="star-wrapper">
								<star :size="24" :score="rating.score"></star>
								<span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
							</div>
							<p class="text">{{rating.text}}</p>
							<div class="recommend" v-show="rating.recommend && rating.recommend.length">
								<span class="icon-thumb_up"></span>
								<span v-for="item in rating.recommend">{{item}}</span>
							</div>
							<div class="time">{{rating.rateTime | formatDate}}</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script type="text-ecmascript-6">
	import BScroll from 'better-scroll';
	import star from 'components/star/star';
	import split from 'components/split/split';
	import ratingselect from 'components/ratingselect/ratingselect';
	import Vue from 'vue';
	import {formatDate} from 'common/js/date';

	const ERR_OK = 0;
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default {
	  	props: {
	  		seller: {
	  			type:Object
	  		}
	  	},
	    data() {
			return {
				ratings: [],
				showFlag: false,
				selectType: ALL,
				onlyContent: true
			}
		},
	    created() {
	    	this.$http.get('/api/ratings').then(response => {
			// success callback
			response = response.body;
				if(response.errno === ERR_OK) {
					this.ratings = response.data;
					this.$nextTick(() => {

					})
				}
			}, response => {
				// error callback
				console.log('goods--error');
			});
	    },
	    filters: {
			formatDate(time) {
				let date = new Date(time);
				return formatDate(date,'yyyy-MM-dd hh:mm');
			}
		},
	    components: {
	    	star,
	    	split,
	    	ratingselect
		}
	};
</script>
<style lang="scss">
	@import "../../common/scss/mixin.scss";

	.ratings {
		position: absolute;
		@include px2rem('top', 352px);
	    bottom: 0;
	    width: 100%;
	    overflow: hidden;

		.overview {
			display: flex;
			@include px2rem('padding', 36px 0px);
			.overview-left {
				@include px2rem('width', 275px);
				border-right: 1px solid rgba(7,17,27,0.1);
				text-align: center;
				@media only screen and (max-width: 320px) {
					@include px2rem('width', 240px);
				}
				.score {
					@include px2rem('margin-bottom', 12px);
					@include px2rem('font-size', 24px);
					@include px2rem('line-height', 56px);
					color: rgb(255,153,0);
				}
				.title {
					@include px2rem('margin-bottom', 16px);
					@include px2rem('font-size', 24px);
					@include px2rem('line-height', 24px);
					color: rgb(7,17,27);
				}
				.rank {
					@include px2rem('margin-bottom', 12px);
					@include px2rem('font-size', 20px);
					@include px2rem('line-height', 20px);
					color: rgb(147,153,159);
				}

			}

			.overview-right {
				flex: 1;
				@include px2rem('padding-left', 48px);
				.score-wrapper {
					@include px2rem('line-height', 36px);
					@include px2rem('height', 36px);
					@include px2rem('margin-bottom', 16px);
					overflow: hidden;
					font-size: 0;
					.title {
						vertical-align: top;
						display: inline-block;
						@include px2rem('font-size', 24px);
						color: rgb(7,17,27);
					}
					.star {
						display: inline-block;
						vertical-align: top;
						@include px2rem('margin', 0px 24px);
					}
					.score {
						vertical-align: top;
						display: inline-block;
						@include px2rem('font-size', 24px);
						color: rgb(255,153,0);
					}
				}
				.delivery-wrapper {
					font-size: 0;
					.title {
						display: inline-block;
						@include px2rem('font-size', 24px);
						color: rgb(7,17,27);
					}
					.delivery {
						display: inline-block;
						@include px2rem('font-size', 24px);
						color: rgb(147,153,159);
						@include px2rem('margin-left', 24px);
					}
				}
			}
		}
	}
</style>
