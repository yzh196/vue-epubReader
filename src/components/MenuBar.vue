<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div :class="{'hide-box-shadow': SettingMenushow || !TitleMenushow}" class="menu-wrapper" v-show="TitleMenushow">
        <span class="icon-wrapper"><i @click="showSetting(3)" class="fa-reorder icon"></i></span>
        <span class="icon-wrapper"><i @click="showSetting(2)" class="fa-sliders icon"></i></span>
        <span class="icon-wrapper"><i @click="showSetting(1)" class="fa-sun-o icon"></i></span>
        <span class="icon-wrapper"><i @click="showSetting(0)" class="fa-font icon"></i></span>
      </div>
      '
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper" v-show="SettingMenushow">
        <div class="setting-font-size" v-if="setTarget === 0">
          <div :style="{fontSize: fontSizeList[0] + 'px'}" class="preview">A</div>
          <div class="select">
            <div :key="item" @click="setFontsize(item)" class="select-wrapper" v-for="item in fontSizeList">
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultSize === item">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div :style="{fontSize: fontSizeList[fontSizeList.length - 1] + 'px'}" class="preview">A</div>
        </div>
        <div class="setting-theme" v-else-if="setTarget === 1">
          <div :key="index" class="setting-theme-item" v-for="(item, index) in themeList">
            <div :style="{background: item.style.body.background}" @click="setTheme(index)" class="preview"></div>
            <div :class="{'selected': index === defaultTheme}" class="text">{{item.name}}</div>
          </div>
        </div>
        <div class="setting-progress" v-else-if="setTarget === 2">
          <div class="progress-wrapper">
            <input :disabled="!bookAvailabele" :value="bookProgress" @change="onProgressChange" class="progress"
                   max="100"
                   min="0" ref="progress" step="1" type="range"/>
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailabele ? bookProgress + "%" : '书籍加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view :bookAvailabele="bookAvailabele" :navigation="navigation" @jumpTo="jumpTo"
                  v-show="showContent"></content-view>
    <transition name="fade">
      <div @click="hideContent" class="content-mask" v-show="showContent"></div>
    </transition>
  </div>
</template>

<script>
  import contentView from '@/components/contentview'

  export default {
    name: "MenuBar",
    components: {
      contentView
    },
    props: {
      TitleMenushow: {
        type: Boolean,
        default: false
      },
      fontSizeList: {
        type: Array,
        default: [16]
      },
      defaultSize: {
        type: Number,
        default: 16
      },
      themeList: {
        type: Array
      },
      defaultTheme: {
        type: Number
      },
      bookAvailabele: {
        type: Boolean,
        default: false
      },
      bookProgress: {
        type: Number,
        default: 0
      },
      navigation: Object
    },
    data: function () {
      return {
        SettingMenushow: false,
        setTarget: 0,
        showContent: false
      }
    },
    methods: {
      showSetting: function (tar) {
        if (tar === 3) {
          this.showContent = true;
          this.SettingMenushow = false;
          this.setTarget = tar;
        } else {
          this.SettingMenushow = true;
          this.setTarget = tar;
        }
      },
      hideSetting: function () {
        this.SettingMenushow = false
      },
      hideContent: function () {
        this.showContent = false
      },
      jumpTo: function (href, index) {
        this.$emit('jumpTo', href, index);
      },
      setFontsize: function (fontsize) {
        this.$emit('setFontsize', fontsize);
      },
      setTheme: function (index) {
        this.$emit('setTheme', index);
      },
      onProgressChange: function (event) {
        let progress = parseInt(event.target.value);
        this.$emit('onProgressChange', progress);
        this.onProgressInput(progress);
      },
      onProgressInput: function (progress) {
        let bookPropress = progress;
        this.$refs.progress.style.backgroundSize = `${bookPropress}% 100%`;
      }
    },
    updated: function () {
      let bookPropress = this.bookProgress;
      if (this.$refs.progress) {
        this.$refs.progress.style.backgroundSize = `${bookPropress}% 100%`;
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import '@/assets/styles/global';

  .menu-bar {
    .menu-wrapper {
      position: absolute;
      bottom: 0;
      left: 0;
      z-index: 100;
      display: flex;
      width: 100%;
      height: px2rem(100);
      background: #f9f9f9;
      box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);

      &.hide-box-shadow {
        box-shadow: none;
      }

      .icon-wrapper {
        flex: 1;
        @include center;

        .icon-progress {
          font-size: px2rem(28);
        }

        .icon-bright {
          font-size: px2rem(24);
        }
      }
    }

    .setting-wrapper {
      position: absolute;
      bottom: px2rem(100);
      left: 0;
      z-index: 101;
      width: 100%;
      height: px2rem(120);
      background: #f7f7f7;
      box-shadow: 0 px2rem(-8) px2rem(8) rgba(0, 0, 0, .15);

      .setting-font-size {
        display: flex;
        height: 100%;

        .preview {
          flex: 0 0 px2rem(100);
          @include center;
        }

        .select {
          display: flex;
          flex: 1;

          .select-wrapper {
            flex: 1;
            display: flex;
            align-items: center;

            &:first-child {
              .line {
                &:first-child {
                  border-top: none;
                }
              }
            }

            &:last-child {
              .line {
                &:last-child {
                  border-top: none;
                }
              }
            }

            .line {
              flex: 1;
              height: 0;
              border-top: 1px solid #ccc;
            }

            .point-wrapper {
              position: relative;
              flex: 0 0 0;
              width: 0;
              height: px2rem(14);
              border-left: 2px solid #bebebe;
              cursor: pointer;

              .point {
                position: absolute;
                top: px2rem(-16);
                left: px2rem(-16);
                width: px2rem(40);
                height: px2rem(40);
                border-radius: 50%;
                background: white;
                border: 1px solid #ccc;
                box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, .15);
                @include center;

                .small-point {
                  width: px2rem(10);
                  height: px2rem(10);
                  background: black;
                  border-radius: 50%;
                }
              }
            }
          }
        }
      }

      .setting-theme {
        height: 100%;
        display: flex;

        .setting-theme-item {
          flex: 1;
          display: flex;
          flex-direction: column;
          padding: px2rem(8);
          box-sizing: border-box;

          .preview {
            flex: 1;
            border: px2rem(1) solid #ccc;
            box-sizing: border-box;

            &.no-border {
              border: none;
            }
          }

          .text {
            flex: 0 0 px2rem(40);
            font-size: px2rem(30);
            color: #ccc;
            @include center;

            &.selected {
              color: #333;
            }
          }
        }
      }

      .setting-progress {
        position: relative;
        width: 100%;
        height: 100%;

        .progress-wrapper {
          width: 100%;
          height: 100%;
          @include center;
          padding: 0 px2rem(30);
          box-sizing: border-box;

          .progress {
            width: 100%;
            -webkit-appearance: none;
            height: px2rem(2);
            background: -webkit-linear-gradient(#999, #999) no-repeat, #ddd;
            background-size: 0 100%;

            &:focus {
              outline: none;
            }

            &::-webkit-slider-thumb {
              -webkit-appearance: none;
              height: px2rem(30);
              width: px2rem(30);
              border-radius: 50%;
              background: white;
              box-shadow: 0 4px 4px 0 rgba(0, 0, 0, .15);
              border: px2rem(2) solid #ddd;
              cursor: pointer;
            }
          }
        }

        .text-wrapper {
          position: absolute;
          left: 0;
          bottom: 0;
          width: 100%;
          color: #333;
          font-size: px2rem(12);
          text-align: center;
        }
      }
    }

    .content-mask {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 101;
      display: flex;
      width: 100%;
      height: 100%;
      background: rgba(51, 51, 51, .8);
    }
  }
</style>
