<template>
  <transition name="slide-right">
    <div class="content">
      <div class="content-wrapper" v-if="bookAvailabele">
        <div :key="index" @click="jumpTo(item.href, index)" class="content-item"
             v-for="(item, index) in navigation.toc">
          <span class="text">{{item.label}}</span>
        </div>
      </div>
      <div class="empty" v-else="bookAvailabele">加载中...</div>
    </div>
  </transition>
</template>

<script>
  export default {
    name: "contentView",
    props: {
      navigation: Object,
      bookAvailabele: {
        type: Boolean,
        default: false
      }
    },
    methods: {
      jumpTo: function (href, index) {

        this.$emit('jumpTo', href, index);
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import "@/assets/styles/global";

  .content {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 102;
    width: 80%;
    height: 100%;
    background: white;

    .content-wrapper {
      width: 100%;
      height: 100%;
      overflow: auto;

      .content-item {
        padding: px2rem(24) px2rem(15);
        border-bottom: px2rem(2) solid #eee;

        .text {
          font-size: px2rem(36);
          font-weight: 600;
          color: #333;
        }

      }
    }

    .empty {
      width: 100%;
      height: 100%;
      @include center;
      font-size: px2rem(16);
      color: #333;
    }

  }
</style>
