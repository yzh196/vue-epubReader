<template>
  <div class="ebook">
    <Title :TitleMenushow="TitleMenushow"></Title>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleMenuShow"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <Menu :TitleMenushow="TitleMenushow" :fontSizeList="fontSizeList" :defaultSize="defaultSize"
          :themeList="themeList" :defaultTheme="defaultTheme" @setTheme="setTheme" @setFontsize="setFontsize"
          :bookAvailabele="bookAvailabele" :bookProgress="bookProgress" :navigation="navigation" @jumpTo="jumpTo"
          @onProgressChange="onProgressChange"
          ref="Menu"></Menu>
  </div>
</template>

<script>
  import Epub from 'epubjs';
  import Title from '@/components/TitleBar';
  import Menu from '@/components/MenuBar'

  export default {
    name: 'home',
    Epub,
    data() {
      return {
        book: null,
        rendition: null,
        BOOK_URL: 'ebooks/2018_Book_AgileProcessesInSoftwareEngine.epub',
        BOOK_URL2: 'ebooks/2019.12.5.epub',
        TitleMenushow: false,
        fontSizeList: [14, 16, 18, 20, 22, 24],
        defaultSize: 16,
        themeList: [
          {
            name: 'default',
            style: {
              body: {
                'color': "#000",
                'background': "#fff"
              }
            }
          },
          {
            name: 'eye',
            style: {
              body: {
                'color': "#371f1b",
                'background': "#109c12",
                'font-family': '宋体'
              }
            }
          },
          {
            name: 'night',
            style: {
              body: {
                'color': "#fff",
                'background': "#000"
              }
            }
          },
          {
            name: 'gold',
            style: {
              body: {
                'color': "#000",
                'background': "rgba(241,236,226)"
              }
            }
          }
        ],
        defaultTheme: 0,
        navigation: null,
        bookAvailabele: false,
        bookProgress: 10
      }
    },
    components: {
      Title,
      Menu
    },
    methods: {
      showPub: function () {
        //this.book = new Epub('./ebooks/2018_Book_AgileProcessesInSoftwareEngine.epub');
        this.book = new Epub(this.BOOK_URL);
        this.rendition = this.book.renderTo('read', {
          width: window.innerWidth,
          height: window.innerHeight,
          method: 'default'
        });
        this.rendition.display();
        this.themes = this.rendition.themes;
        this.rendition.themes.fontSize(this.defaultSize);
        this.registerTheme();
        this.setTheme(this.defaultTheme);

        this.book.ready.then(() => {
          this.navigation = this.book.navigation;
          return this.book.locations.generate();

        }).then(result => {
          this.locations = this.book.locations;
          this.bookAvailabele = true;
          if (this.bookProgress > 0) {
            this.onProgressChange(this.bookProgress);
          }
        })

      },
      jumpTo: function (href, index) {
        this.rendition.display(href);
        this.hideTitleandMenu();
        let num = this.rendition.location.start.location;
        this.bookProgress = (num / this.locations.total) * 100;
        this.bookProgress = parseFloat(this.bookProgress.toFixed(2));
      },
      hideTitleandMenu: function () {
        this.TitleMenushow = false;
        this.$refs.Menu.hideSetting();
        this.$refs.Menu.hideContent();
      },
      onProgressChange: function (progress) {
        let percentage = progress / 100;
        let location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
        this.rendition.display(location);
        this.bookProgress = progress;
      },
      prevPage: function () {
        if (this.rendition) {
          this.rendition.prev()
        }
      },
      nextPage: function () {
        if (this.rendition) {
          this.rendition.next()
        }
      },
      toggleTitleMenuShow: function () {
        this.TitleMenushow = !this.TitleMenushow;
        if (!this.TitleMenushow) {
          this.$refs.Menu.hideSetting()

        }
      },
      setFontsize: function (fontsize) {
        if (this.themes) {
          this.defaultSize = fontsize;
          this.themes.fontSize(fontsize + 'px');
        }
      },
      registerTheme: function () {
        this.themeList.forEach(item => {
          this.themes.register(item.name, item.style)
        })

      },
      setTheme: function (index) {
        this.themes.select(this.themeList[index].name);
        this.defaultTheme = index;
      }
    },
    mounted: function () {
      this.showPub();
    }
  }
</script>
<style lang="scss" scoped>
  @import "@/assets/styles/global.scss";

  .ebook {
    position: relative;
    overflow: hidden;

    .read-wrapper {
      .mask {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 100;
        display: flex;
        width: 100%;
        height: 100%;

        .left {
          flex: 0 0 px2rem(600);
        }

        .center {
          flex: 1;
        }

        .right {
          flex: 0 0 px2rem(600);
        }
      }
    }
  }
</style>

