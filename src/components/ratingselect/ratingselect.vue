<template>
	<div class="ratingselect">
		<div class="rating-type">
			<span class="block positive" :class="{'active':selectType===2}" @click="select(2,$event)">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
			<span class="block positive" :class="{'active':selectType===0}"  @click="select(0,$event)">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
 			<span class="block negative" :class="{'active':selectType===1}"  @click="select(1,$event)">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
		</div>
		<div @click="toggleContent($event)" class="switch" :class="{'on':onlyContent}">
			<span class="icon-check_circle"></span>
			<span class="text">只看有内容的评价</span>
		</div>
	</div>
</template>
<script type="text-ecmascript-6">
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default {
		props: {
			ratings: {
				type: Array,
				default() {
					return [];
				}
			},
			selectType: {
				type: Number,
				default: ALL
			},
			onlyContent: {
				type: Boolean,
				default: false
			},
			desc: {
				type: Object,
				default() {
					return {
						all: '全部',
						positive: '满意',
						negative: '不满意'
					};
				}
			}
		},
		computed: {
			positives() {
				return this.ratings.filter((rating) => {
					return rating.rateType === POSITIVE;
				})
			},
			negatives() {
				return this.ratings.filter((rating) => {
					return rating.rateType === NEGATIVE;
				})
			}
		},
		methods: {
			select(type,event) {
				if(!event._constructed) {//这个属性，浏览器原生的是没有这个属性的
	        		return false;
	        	} 
	        	this.selectType = type;
	        	//通过派发事件去改变父组件的selectType
	        	this.$dispatch('ratingtype.select',this.selectType);
			},
			toggleContent(event) {
				if(!event._constructed) {//这个属性，浏览器原生的是没有这个属性的
	        		return false;
	        	} 
	        	this.onlyContent = !this.onlyContent;
	        	this.$dispatch('content.toggle',this.onlyContent);
			}
		}
	}
</script>
<style lang="scss">
	@import "../../common/scss/mixin.scss";
	.ratingselect {
		
		.rating-type {
			@include px2rem(padding, 36px 0px);
			@include px2rem(margin, 0px 36px);
			border-bottom: 1px solid rgba(7,17,27,0.1);
			font-size: 0;
			.block {
				display: inline-block;
				@include px2rem(padding, 16px 24px);
				@include px2rem(margin-right, 16px);
				@include px2rem(font-size, 24px);
				@include px2rem(line-height, 32px);
				border-radius: 2px;
				color: rbg(77,85,93);
				&.active {
					color: #fff;
				}
				&.positive {
					background: rgba(0,160,220,0.2);
					&.active {
						background: rgb(0,160,220);
					}
				}
				&.negative {
					background: rgba(77,85,93,0.2);
					&.active {
						background: rgb(77,85,93);
					}
				}

				.count {
					@include px2rem(font-size, 16px);
					@include px2rem(margin-left, 4px);
				}
			}
		}
		.switch {
			@include px2rem(padding, 24px 36px);
			@include px2rem(line-height, 48px);
			border-bottom: 1px solid rgba(7,17,27,0.1);
			color: rgb(147,153,159);
			&.on {
				.icon-check_circle {
					color: #00c850;
				}
			}
			.icon-check_circle {
				vertical-align: top;
				display: inline-block;
				@include px2rem(font-size, 48px);
				@include px2rem(margin-right, 8px);
			}
			.text {
				vertical-align: top;
				display: inline-block;
				@include px2rem(font-size, 24px);
			}
		}
	}
</style>