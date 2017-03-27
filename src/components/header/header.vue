<template>
	<div class="header">
     <div class="content-wrapper">
       <div class="avatar">
         <img :src="seller.avatar">
       </div>

       <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
       </div>
     </div>


  </div>
</template>

<script type="text/ecmascript-6">
export default {
	props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {};
  }
};
</script>

<style lang="scss" rel="stylesheet/scss">
	.header {
    width: 200px;
    height: 200px;
    background:red;
  }
</style>