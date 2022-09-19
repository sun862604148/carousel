<template>
  <div :class="`swiper mode-${mode}`">
    <div class="swiper-wrapper">
      <div
        v-for="(item, index) in bannerList"
        :data-href="item.link"
        :key="index"
        class="swiper-slide"
      >
        <div class="banner">
          <video v-if="item.type === 'video'" class="video" :src="item.src" muted autoplay loop></video>
          <img class="image" v-else-if="item.type === 'image'" :src="item.src" />

          <div :class="['slogan', {'mode-difference': magic}]">
            <div v-if="item.headline" v-html="item.headline"></div>
            <div v-if="item.sub" v-html="item.sub"></div>
            <div v-if="item.buttons && item.buttons.length" class="button-container">
              <a
                class="button"
                v-for="(btn, index) in item.buttons"
                :key="index"
                :target="btn.target"
                :href="btn.type === 'route' ? btn.to : 'javascript:(0)'"
                :onclick="btn.type === 'callback' && btn.callback"
              >{{btn.text}}</a>
            </div>
            <div v-if="item.links && item.links.length" class="link-container">
              <a
                class="link"
                v-for="(link, index) in item.buttons"
                :key="index"
                :target="link.target"
                :href="link.to"
              >{{link.text}} <img src="./arrow-right.svg" /></a>
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- Add Pagination -->
    <div class="swiper-pagination"></div>
    <div class="swiper-button-next"></div>
    <div class="swiper-button-prev"></div>
  </div>
</template>

<script>
import Swiper, { Pagination, Autoplay, Navigation } from 'swiper'
import 'swiper/swiper-bundle.css'
import imagePng from './image.png'

Swiper.use(Pagination)
Swiper.use(Autoplay)
Swiper.use(Navigation)

export default {
  props: {
    mode: {
      type: String,
      default: 'normal'
    },
    duration: {
      type: Number,
      default: 3000
    },
    indicator: {
      type: String,
      default: 'countdown'
    },
    data: {
      type: Array,
      default: () => { return [] }
    },
    magic: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      mySwiper: null,
      bannerList: [{
          name: 'Bar Party1', // string，用于 roll 形式的指示器
          type: 'image', // string, 背景类型，image/video,
          src: imagePng, // string, 背景源
        },{
          name: 'Bar Party2', // string，用于 roll 形式的指示器
          type: 'image', // string, 背景类型，image/video,
          src: imagePng, // string, 背景源
        },
        {
          name: 'Bar Party3', // string，用于 roll 形式的指示器
          headline: '<div>Bar Party</div>', // string，用于大标题，v-html 绑定，可以自定义字号尺寸
          sub: '<div>12.12 20:00 | Sweet Bistro, Beijing</div>', // string，用于副标题，v-html 绑定，可以自定义字号尺寸
          type: 'image', // string, 背景类型，image/video,
          src: imagePng, // string, 背景源
          buttons: [ // 按钮，用于渲染按钮
            {
              text: 'Reserve Now', // 按钮文字
              type: 'route', // 按钮功能：route - 路由跳转，link - 网页跳转，callback - 回调函数
              to: '/activity/bar-party-1212', // 跳转目标
              target: '_blank' // 是否打开新窗口
            },
            {
              text: 'Reserve Now',
              type: 'callback',
              callback: () => { // 回调函数
                console.log('callback executed')
              }
            }
          ],
          links: [ // 链接，用于渲染链接
            {
              text: 'Learn more',
              type: 'route',
              to: '/activity/bar-party-1212',
              target: '_blank'
            },
            {
              text: 'Reserve Now',
              type: 'route',
              to: '/reserve/bar-party-1212',
              target: '_blank'
            }
          ]
        }
      ]
    }
  },
  computed: {
    spaceBetween() {
      return this.mode === 'card' ? 20 : 0
    },
    rollPaginationList() {
      const list = this.bannerList.map(item => item.name);
      return list;
    }
  },
  methods: {
    renderCountdownPagination(current, total) {
      let paginationHtml = '';
      for(let i = 0; i < total; i++) {
        // 判断是否是激活焦点，是的话添加active类，不是就只添加基本样式类
        if (i === (current -1)) {
          paginationHtml += `<span class="swiper-pagination-customs swiper-pagination-customs-active">
            <span style="animation-duration: ${this.duration + 500}ms"></span>
          </span>`;
        } else {
          paginationHtml += '<span class="swiper-pagination-customs"></span>';
        }
      }
      return paginationHtml;
    },
    renderRollPagination(current, total) {
      const originList = this.rollPaginationList;
      const rollList = [originList[originList.length - 1], ...originList, originList[0]]
      const pre = rollList[current - 1];
      const curr = rollList[current];
      const next = rollList[current + 1];
      const paginationHtml = `
        <span class="swiper-pagination-roll"></span>
        <span class="swiper-pagination-roll swiper-pagination-roll-active"></span>
        <span class="swiper-pagination-roll"></span>
      `;
      return paginationHtml;
    },
    init() {
      this.mySwiper = new Swiper('.swiper', {
        loop: true,
        spaceBetween: this.spaceBetween,
        slidesPerView: 'auto',
        centeredSlides: true,
        autoplay: {
          delay: this.duration,
        },
        pagination: {
          el: '.swiper-pagination',
          clickable: true,
          type: 'custom',
          renderCustom: (swiper, current, total) => {
            return this.renderCountdownPagination(current, total);
          }
        },
        navigation: {
          nextEl: ".swiper-button-next",
          prevEl: ".swiper-button-prev",
        },
      });
    }
  },
  mounted() {
    this.init();
  },
  beforeDestroy() {
    this.mySwiper && this.mySwiper.destroy()
  }
}
</script>
<style lang="less" scoped>
.swiper {
  position: relative;
  height: 100%;
  width: 100%;
  height: 400px;
  overflow: hidden;
  :deep(.swiper-pagination) {
    bottom: 15Px;
  }
  :deep(.swiper-pagination-customs) {
    display: inline-block;
    width: 4Px;
    height: 4Px;
    background: rgba(255, 255, 255, 0.6);
    border-radius: 2px;
    transition: all, 300ms;
    margin: 0 4px;
    cursor: pointer;
  }
  @keyframes activeStyle {
    from {
      width: 0;
    }
    to {
      width: 100%;
    }
  }
  :deep(.swiper-pagination-customs-active) {
    position: relative;
    width: 30Px;
    border-radius: 5px;
    overflow: hidden;
    span {
      display: inline-block;
      position: absolute;
      left: 0;
      height: 100%;
      width: 100%;
      animation: activeStyle 3000ms infinite;
      background: #FFF;
    }
  }
}
.mode-card {
  .swiper-slide {
    width: 70%;
    overflow: hidden;
  }
}
.banner {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
  .video {
    min-width: 100%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 0;
  }
  .image {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
}
.mode-difference {
  mix-blend-mode: difference;
}
.slogan {
  position: absolute;
  top: 100px;
  left: 50%;
  color: #FFF;
  font-family: Impact;
  text-align: center;
  vertical-align: top;
  transform: translateX(-50%);
  z-index: 9;
  .hide-link-style {
    display: flex;
    width: 150Px;
  }
  .button-container,
  .link-container {
    margin-top: 22px;
  }
  .button-container .button {
    display: inline-block;
    height: 40px;
    line-height: 40px;
    font-size: 14px;
    color: #FFF;
    border: 1px solid #FFF;
    border-radius: 20px;
    padding: 0 22px;
    text-decoration: none;
    box-sizing: border-box;
    &:not(:last-child) {
      margin-right: 20px;
    }
  }
  .link-container .link {
    font-weight: 500;
    font-size: 14px;
    color: #FFF;
    line-height: 20px;
    &:not(:last-child) {
      margin-right: 30px;
    }
    img {
      vertical-align: middle;
    }
  }
}
</style>