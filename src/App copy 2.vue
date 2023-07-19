<template>
  <div id="app">
    <div ref="list" class="photo-wall">
      <div v-for="item in list" :key="'no' + item.key" :ref="`box${item.key}`" :class="['box', 'box' + item.key]"
        :style="{ ...item, padding }">
        <div class="img" @click="(e) => handleClick(e, item)">
          <!-- <img :src="item.src" /> -->
        </div>
      </div>
    </div>
    <div :class="['mask', show ? 'active' : '']" @click="show = false">
      <div class="info">123</div>
    </div>
    <div :class="['photo', show ? 'active' : '']" ref="photo" :style="{ padding }">
      <div class="img">
        <img :src="showSrc" />
      </div>
    </div>
    <div class="drop" ref="drop"></div>
  </div>
</template>

<script>

const wh = {
  1: {
    width: 5,
    height: 7
  },
  2: {
    width: 80,
    height: 110
  },
  3: {
    width: 120,
    height: 160
  },
  4: {
    width: 160,
    height: 220
  },
  5: {
    width: 9,
    height: 12
  }
}

import img1 from '@/assets/1.jpeg'
import img2 from '@/assets/2.jpeg'
import img3 from '@/assets/3.jpeg'
import img4 from '@/assets/4.jpeg'
import img5 from '@/assets/5.jpeg'
// import * as TWEEN from '@tweenjs/tween.js'
export default {
  name: 'App',
  props: {
    padding: {
      type: String,
      default: '3px'
    }
  },
  data() {
    return {
      //
      list: [],
      param: {},
      // 屏幕中间值
      mid: 0,
      // 一个大组的长度
      piece: 0,
      // 显示蒙版
      show: false,
      initStyle: {

      },
      // 显示图片src
      showSrc: '',
    }
  },
  watch: {
    show(data) {
      if (!data) {
        const { width, height, top, bottom, left } = this.initStyle
        this.$refs.photo.style.width = width
        this.$refs.photo.style.height = height
        this.$refs.photo.style.left = left
        if (top) {
          this.$refs.photo.style.top = top
          this.$refs.photo.style.bottom = 'inherit'
        } else {
          this.$refs.photo.style.bottom = bottom
          this.$refs.photo.style.top = 'inherit'
        }
        setTimeout(() => {
          this.$refs.photo.style.left = 'inherit'
          this.$refs.photo.style.top = 'inherit'
          this.$refs.photo.style.bottom = 'inherit'
          this.$refs.photo.style['transition-duration'] = 0
        }, 601);
      }
    }
  },
  mounted() {
    this.mid = window.innerHeight
    this.init()
    this.CreateGos()
    window.angle = this.angle
  },
  methods: {
    init() {
      const total = (...agrm) => {
        return agrm.reduce((x, y) => x + y, 0)
      }

      const g1 = 0
      const g2 = g1 + total(wh[2].width * 2)
      const g3 = g2 + total(wh[3].width)
      const g4 = g3 + total(wh[2].width)
      const g5 = g4 + total(wh[2].width * 2)
      const g6 = g5 + total(wh[3].width * 2)
      const g7 = g6 + total(wh[4].width + wh[2].width)
      const g8 = g7 + total(wh[3].width * 2)

      this.piece = g8 + total(wh[3].width * 2)

      this.param = {
        '-6': {
          type: 3,
          direction: 0,
          x: 0,
          y: 0
        },
        '-5': {
          type: 3,
          direction: 0,
          x: total(wh[3].width),
          y: 0
        },
        '-4': {
          type: 2,
          direction: 0,
          x: 0,
          y: total(wh[3].height)
        },
        '-3': {
          type: 2,
          direction: 0,
          x: total(wh[2].width),
          y: total(wh[3].height)
        },
        '-2': {
          type: 2,
          direction: 0,
          x: total(wh[2].width * 2),
          y: total(wh[3].height)
        },
        '-1': {
          type: 3,
          direction: 0,
          x: total(wh[3].width),
          y: total(wh[3].height + wh[2].height)
        },
        // 组合1
        '0': {
          type: 2,
          x: g1,
          y: 0
        },
        '1': {
          type: 2,
          x: g1 + total(wh[2].width),
          y: 0
        },
        '2': {
          type: 4,
          x: g1,
          y: total(wh[2].height)
        },
        '3': {
          type: 2,
          x: g1,
          y: total(wh[2].height + wh[4].height)
        },
        '4': {
          type: 2,
          x: g1 + total(wh[2].width),
          y: total(wh[2].height + wh[4].height)
        },
        // 组合2
        '5': {
          type: 3,
          x: g2,
          y: 0
        },
        '6': {
          type: 3,
          x: g2,
          y: total(wh[3].height)
        },
        '7': {
          type: 3,
          x: g2,
          y: total(wh[3].height * 2)
        },
        // 组合3
        '8': {
          type: 2,
          x: g3,
          y: 0
        },
        '9': {
          type: 2,
          x: g3,
          y: total(wh[2].height)
        },
        '10': {
          type: 2,
          x: g3,
          y: total(wh[2].height * 2)
        },
        '11': {
          type: 2,
          x: g3,
          y: total(wh[2].height * 3)
        },
        '12': {
          type: 2,
          x: g3,
          y: total(wh[2].height * 4)
        },
        // 组合4
        '13': {
          type: 2,
          x: g4,
          y: 0
        },
        '14': {
          type: 2,
          x: g4 + total(wh[2].width),
          y: 0
        },
        '15': {
          type: 4,
          x: g4,
          y: total(wh[2].height)
        },
        '16': {
          type: 2,
          x: g4,
          y: total(wh[2].height + wh[4].height)
        },
        // 组合5
        '17': {
          type: 3,
          x: g5,
          y: 0
        },
        '18': {
          type: 3,
          x: g5 + total(wh[3].width),
          y: 0
        },
        '19': {
          type: 2,
          x: g5,
          y: total(wh[3].height)
        },
        '20': {
          type: 2,
          x: g5 + total(wh[2].width),
          y: total(wh[3].height)
        },
        '21': {
          type: 2,
          x: g5 + total(wh[2].width * 2),
          y: total(wh[3].height)
        },
        '22': {
          type: 3,
          x: g5,
          y: total(wh[3].height + wh[2].height)
        },
        '23': {
          type: 3,
          x: g5 + total(wh[3].width),
          y: total(wh[3].height + wh[2].height)
        },
        // 组合6
        '24': {
          type: 4,
          x: g6,
          y: 0
        },
        '25': {
          type: 2,
          x: g6 + total(wh[4].width),
          y: 0
        },
        '26': {
          type: 2,
          x: g6 + total(wh[4].width),
          y: total(wh[2].height)
        },
        '27': {
          type: 2,
          x: g6,
          y: total(wh[4].height)
        },
        '28': {
          type: 2,
          x: g6,
          y: total(wh[4].height + wh[2].height)
        },
        '29': {
          type: 4,
          x: g6 + total(wh[2].width),
          y: total(wh[4].height)
        },
        '30': {
          type: 4,
          x: g6,
          y: total(wh[4].height + wh[2].height * 2)
        },
        // 组合7
        '31': {
          type: 3,
          x: g7,
          y: 0
        },
        '32': {
          type: 3,
          x: g7 + total(wh[3].width),
          y: 0
        },
        '33': {
          type: 3,
          x: g7,
          y: total(wh[3].height)
        },
        '34': {
          type: 3,
          x: g7 + total(wh[3].width),
          y: total(wh[3].height)
        },
        '35': {
          type: 3,
          x: g7,
          y: total(wh[3].height * 2)
        },
        '36': {
          type: 3,
          x: g7 + total(wh[3].width),
          y: total(wh[3].height * 2)
        },
        '37': {
          type: 3,
          x: g7,
          y: total(wh[3].height * 3)
        },
        '38': {
          type: 3,
          x: g7 + total(wh[3].width),
          y: total(wh[3].height * 3)
        },
        // 组合8
        '39': {
          type: 3,
          x: g8,
          y: 0
        },
        '40': {
          type: 3,
          x: g8 + total(wh[3].width),
          y: 0
        },
        '41': {
          type: 2,
          x: g8,
          y: total(wh[3].height)
        },
        '42': {
          type: 2,
          x: g8 + total(wh[2].width),
          y: total(wh[3].height)
        },
        '43': {
          type: 2,
          x: g8 + total(wh[2].width * 2),
          y: total(wh[3].height)
        },
        '44': {
          type: 3,
          x: g8,
          y: total(wh[3].height + wh[2].height)
        },
        '45': {
          type: 3,
          x: g8 + total(wh[3].width),
          y: total(wh[3].height + wh[2].height)
        },
      }
    },
    CreateGos() {
      const list = new Array(300).fill(1)
      this.list = list.map((el, i) => {
        let item = {};
        let key = i - 6;
        let index = key
        if (key >= 0) {
          index = Math.floor(key % 92 / 2)
        }
        const { type, direction, x, y } = this.param[index]
        const { width, height } = wh[type]
        item = {
          key,
          width: width + 'px',
          height: height + 'px',
          left: x + 'px',
          sl: 100 + +x + 'px',
          // 'transition-delay': `${i * 5}ms`,
          'animation-delay': `${i * 5}ms`,// `${Math.floor(Math.random() * 1000)}ms`,
          src: { 0: img1, 1: img2, 2: img3, 3: img4, 4: img5 }[i % 5],
          origin: {
            x: x + width / 2,
            y: 0
          }

        }
        let _d = direction;
        if (key >= 0) {
          // 判断上/下
          _d = key % 2 ? 0 : 1
          // 判断组别
          const _k = Math.floor(key / 92)
          // left的值
          let l = 0
          // 0 上 1 下
          if (_d === 0) {
            l = wh[3].width * 2 + +x + (this.piece * _k)
          } else {
            l = +x + (this.piece * _k)
          }
          // 设置left的值 和 中心点x的值
          item.left = l + 'px'
          item.origin.x = l + width / 2
        }
        // 0 上 1 下
        if (_d === 0) {
          // bottom的值
          const b = this.mid + +y
          item.bottom = b + 'px'
          // 中心点top的值 bottm 转化top
          item.origin.y = 100 - b - height / 2
        } else {
          // top的值
          const t = this.mid + +y
          item.top = t + 'px'
          // 中心点top的值
          item.origin.y = t + height / 2
        }
        return item
      })
    },
    // handleClick(e, item) {
    //   const { scrollLeft } = this.$refs.list;
    //   const { width, height, top, bottom, left, src } = item
    //   // 设置元素的初始
    //   const initLeft = `calc(${left} - ${scrollLeft + 'px'})`
    //   this.$refs.photo.style.width = width
    //   this.$refs.photo.style.height = height
    //   this.$refs.photo.style.left = initLeft
    //   const initStyle = {
    //     width,
    //     height,
    //     left: initLeft
    //   }
    //   if (top) {
    //     this.$refs.photo.style.top = top
    //     this.$refs.photo.style.bottom = 'inherit'
    //     initStyle.top = top
    //   } else {
    //     this.$refs.photo.style.bottom = bottom
    //     this.$refs.photo.style.top = 'inherit'
    //     initStyle.bottom = bottom
    //   }
    //   this.showSrc = src
    //   this.initStyle = initStyle
    //   this.show = true;
    //   this.$nextTick(() => {
    //     const { naturalWidth, naturalHeight } = e.target
    //     let w = 0, h = 0;
    //     if (500 > naturalHeight) {
    //       w = naturalWidth
    //       h = naturalHeight
    //     } else {
    //       const ratio = 500 / naturalHeight
    //       w = naturalWidth * ratio
    //       h = 500
    //     }
    //     setTimeout(() => {
    //       this.$refs.photo.style.width = w + 'px'
    //       this.$refs.photo.style.height = h + 'px'
    //       this.$refs.photo.style['transition-duration'] = `600ms`
    //       this.$refs.photo.style.left = `calc(35vw - ${w / 2 + 'px'})`
    //       if (top) {
    //         this.$refs.photo.style.top = `calc(50vh - ${h / 2 + 'px'})`
    //         this.$refs.photo.style.bottom = 'inherit'
    //       } else {
    //         this.$refs.photo.style.top = 'inherit'
    //         this.$refs.photo.style.bottom = `calc(50vh - ${h / 2 + 'px'})`
    //       }
    //     }, 1);

    //   })

    // }
    angle(start, end) {
      const diff_x = end.x - start.x;
      const diff_y = end.y - start.y;
      //返回角度,不是弧度
      const deg = 180 / Math.PI * Math.atan2(diff_y, diff_x)
      const radianS = Math.sin(deg * Math.PI / 180)
      const radianC = Math.cos(deg * Math.PI / 180)
      // 
      return {
        deg,
        radianS,
        radianC,
      }
    },
    handleClick(e, item) {
      const baseDistance = 20
      const { key, origin: { x, y } } = item
      this.$refs.drop.style.left = x + 'vh';
      this.$refs.drop.style.top = y + 'vh';
      this.$refs[`box${key}`][0].style.setProperty('transform', 'scale(1.5)')
      this.list.map(({ key: ik, origin }) => {
        const { x: ix, y: iy } = origin;
        const w = Math.abs(x - ix)
        const h = Math.abs(y - iy)
        const len = Math.sqrt(Math.pow(w, 2) + Math.pow(h, 2))
        // y = 30 iy = 20
        const { deg: d, radianS, radianC } = this.angle({ x, y }, { x: ix, y: iy > y ? y * 2 - iy : iy * 2 })
        let _d = d < 0 ? 360 + d : d;
        let deg = _d <= 360 ? _d : _d % 360
        if (len < baseDistance && ik != key) {
          if (ik !== key) {
            const sub = (baseDistance - len) / 2;
            if (ik == 141) {
              console.log(deg, radianC * sub, radianS * sub)
            }
            // this.$refs[`box${ik}`][0].style.width = wh[2].width + 'vh'
            // this.$refs[`box${ik}`][0].style.height = wh[2].height + 'vh'
            this.$refs[`box${ik}`][0].style.setProperty('transform', `translate(${radianC * sub}vh,${deg > 180 && deg < 360 ? Math.abs(radianS * sub) : -(radianS * sub)}vh)`)
          }
        } else {
          this.$refs[`box${ik}`][0].style.setProperty('transform', `translate(0,0)`)
        }
      })
    }

  },
  randomRange(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min
  },


}
</script>

<style lang="scss">
html,
body {
  height: 100%;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  height: 100%;
}

.photo-wall {
  position: relative;
  height: 100%;
  width: 100%;
  transform-style: preserve-3d;
  perspective: 800px;
  overflow: auto;

  &::-webkit-scrollbar {
    display: none;
    /*滚动条整体样式*/
    width: 8px;
    /*高宽分别对应横竖滚动条的尺寸*/
    height: 1px;
  }


  .box {
    position: absolute;
    // border: 1px #efefef solid;
    transition-duration: 600ms;
    transform-origin: 0 center;
    // opacity: 0;
    animation: show 700ms ease-out;
    z-index: 2;

    .img {
      height: 100%;
      background-color: #efefef;

      img {
        display: block;
        width: 100%;
        height: 100%;
        object-fit: cover;
      }
    }

    @keyframes show {
      0% {
        opacity: 0;
        transform: rotateY(-45deg);
      }

      100% {
        opacity: 1;
        transform: rotateY(0);
      }
    }
  }

}

.mask {
  position: fixed;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  transition-duration: 400ms;
  overflow: hidden;
  z-index: 9;
  backdrop-filter: blur(5px);
  background-color: rgba(0, 0, 0, .5);
  pointer-events: none;
  opacity: 0;

  .info {
    position: absolute;
    width: 30%;
    height: 100%;
    right: -15%;
    top: 0;
    background-color: #fff;
    opacity: 0;
    transition-duration: 400ms;
  }

  &.active {
    pointer-events: visible;
    opacity: 1;

    .info {
      opacity: 1;
      right: 0
    }
  }
}

.photo {
  position: absolute;
  border: 1px #efefef solid;
  z-index: 19;

  .img {
    height: 100%;
    background-color: #efefef;

    img {
      display: block;
      width: 100%;
      height: 100%;
      object-fit: cover;
    }
  }
}

.drop {
  position: absolute;
  width: 5px;
  height: 5px;
  background-color: red;
  z-index: 29;
}
</style>
