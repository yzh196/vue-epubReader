<template>
  <transition name="slide-down">
    <div class="title-wrapper" v-show="TitleMenushow">
      <div class="left">
        <span class="icon-wrapper"><i class="fa-angle-double-left icon"></i></span>
      </div>
      <div class="right">
        <span class="icon-wrapper"><i class="fa-shopping-cart icon"></i></span>
        <span class="icon-wrapper"><i class="fa-user icon"></i></span>
        <span class="icon-wrapper"><i class="fa-list icon"></i></span>
      </div>
    </div>
  </transition>
</template>

<script>
  export default {
    name: "TitleBar",
    props: {
      TitleMenushow: {
        type: Boolean,
        default: false
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import '@/assets/styles/global';

  .title-wrapper {
    position: absolute;
    top: 0px;
    left: 0px;
    display: flex;
    z-index: 101;
    width: 100%;
    height: px2rem(100);
    background: #f9f9f9;
    box-shadow: rgba(0, 0, 0, .15) px2rem(8) px2rem(8);

    .left {
      flex-basis: 60px;
      @include center;
    }

    .right {
      flex: 1;
      display: flex;
      justify-content: flex-end;

      .icon-wrapper {
        flex: 0 0 px2rem(100);
        @include center;

      }
    }
  }
</style>
