<template>
	<div class="star" :class="starType">
    <span v-for="item in itemClasses" :class="item" class="star-item" track-by="$index"></span> 
  </div>
</template>

<script type="text-ecmascript-6">
const LENGTH = 5;
const CLS_ON = 'on';
const CLS_HALF = 'half';
const CLS_OFF = 'off';

  export default {
    props: {
      size: {
        type: Number
      },
      score: {
        type: Number
      }
    },
    computed: {
      starType () {
        return 'star-' + this.size;
      },

      itemClasses() {
        let result = [];

        //分数向下取整
        let score = Math.floor(this.score * 2) / 2;
        let hasDecimal = score % 1 !== 0;//是否是小数

        let integer = Math.floor(score);
        for(let i=0; i < integer; i++) {
          result.push(CLS_ON);
        }

        if(hasDecimal) {
          result.push(CLS_HALF);
        }

        while(result.length < LENGTH) {
          result.push(CLS_OFF);
        }

        return result;
      }
    }
  }
</script>

<style lang="scss">
	@import "../../common/scss/mixin.scss";

  .star {
    font-size: 0;
    .star-item {
      display: inline-block;
      background-repeat: no-repeat;
    }
    &.star-48 {
      .star-item {
        @include px2rem('width', 40px);
        @include px2rem('height', 38px);
        @include px2rem('margin-right', 44px);
        background-size: cover;
        &:last-child {
          margin-right: 0;
        }

        &.on{
          @include bg-image('star48_on');
        }
        &.half{
          @include bg-image('star48_half');
        }
        &.off{
          @include bg-image('star48_off');
        }
      }
    }

    &.star-36 {
      .star-item {
        @include px2rem('width', 30px);
        @include px2rem('height', 30px);
        @include px2rem('margin-right', 12px);
        background-size: cover;
        &:last-child {
          margin-right: 0;
        }

        &.on{
          @include bg-image('star36_on');
        }
        &.half{
          @include bg-image('star36_half');
        }
        &.off{
          @include bg-image('star36_off');
        }
      }
    }

    &.star-24 {
      .star-item {
        @include px2rem('width', 20px);
        @include px2rem('height', 20px);
        @include px2rem('margin-right', 6px);
        background-size: cover;
        &:last-child {
          margin-right: 0;
        }

        &.on{
          @include bg-image('star24_on');
        }
        &.half{
          @include bg-image('star24_half');
        }
        &.off{
          @include bg-image('star24_off');
        }
      }
    }
  }
</style>