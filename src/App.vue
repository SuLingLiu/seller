<template>
  <div>
  	<v-header :seller="seller"></v-header>

    <div class="main-tab">
      <div class="tab-item">
        <a href="#" v-link="{path:'/goods'}">商品</a>
      </div>
      <div class="tab-item">
        <a href="#" v-link="{path:'/ratings'}">评论</a>
      </div>
      <div class="tab-item">
        <a href="#" v-link="{path:'/seller'}">商家</a>
      </div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script type="text-ecmascript-6">
import header from 'components/header/header';

const ERR_OK = 0;

export default {
  data() {
    return {
      seller: {}
    };
  },
  created() {
    this.$http.get('/api/seller').then(response => {
    // success callback
      response = response.body;
      if(response.errno === ERR_OK) {
        this.seller = response.data;
      }
    }, response => {
      // error callback
      console.log('seller--error');
    });
  },
  components: {
    'v-header': header
  }
};

</script>

<style lang="scss">
  @import './common/scss/mixin.scss';
  .main-tab {
    display: flex;
    width: 100%;
    @include px2rem('height',80px);
    @include px2rem('line-height',80px);
    border-bottom: 1px solid rgba(7,17,27,0.1);
    .tab-item {
      flex: 1;
      width: 1;
      text-align: center;
      a {
        display: block;
        @include font-dpr(14px);
        color: rgb(77,85,93);
        &:hover, &.active {
          color: rgb(240,20,20);
        }
      }
    }
  }
</style>
