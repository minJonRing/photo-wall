<template>
  <div id="app">
    <div ref="list" class="photo-wall">
      <div v-for="item in initList" :key="'no' + item.key" :ref="`box${item.key}`" :class="['box', 'box' + item.key]"
        :attr-key="item.key" :style="bindStyle(item)">
        <div class="img" @click.stop="handleClick(item)" :attr-key="item.key">
          <!-- <img :src="item.src" /> -->{{ item.key }}
        </div>
      </div>
      <!-- 显示的图片 -->
      <div v-for="(item, index) in showList" :key="'show' + index" :ref="`show` + item.key" class="box-show"
        :style="{ ...bindStyleShow(item) }">
        <div class="img" :attr-key="item.key" @dblclick="handleHidden(item)">
          {{ item.key }}
        </div>
      </div>
    </div>
    <div class="line"></div>
  </div>
</template>

<script>

const wh = {
  1: {
    width: 5,
    height: 7
  },
  2: {
    width: 52,
    height: 70
  },
  3: {
    width: 78,
    height: 105
  },
  4: {
    width: 104,
    height: 140
  },
  5: {
    width: 9,
    height: 12
  }
}
import * as TWEEN from '@tweenjs/tween.js'
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
      initList: [],
      list: [],
      animeGroup: {},
      param: {},
      // 屏幕中间值
      mid: 0,
      // 一个大组的长度
      piece: 0,
      // 显示图片src
      showList: [],
      showAnime: {},
      showOption: {
        width: 165,
        height: 220,
        maxRange: 400
      },
      animeOption: {
        // 2点距离小于maxRang 点元素进行移动
        maxRange: 220,
        // 变化速度
        speed: 5
      },
      // 初始化是否结束
      initOver: false,
      updateInterval: 0
    }
  },
  watch: {

  },
  mounted() {
    this.mid = window.innerHeight / 2
    this.init()
    this.createGos(new Array(200).fill(1))
    this.showList = new Array(10).fill({})
    this.$nextTick(() => {
      this.initAnime()
      // 
      this.initHandle()
      this.initScroll()
      setTimeout(() => {
        this.initOver = true
      }, 2000);
    })

    window.angle = this.angle
    requestAnimationFrame(this.animate)
  },
  methods: {
    animate(time) {
      if (time - this.updateInterval > 30) {
        this.initOver && this.animeUpload()
      } else {
        this.updateInterval = time
      }
      requestAnimationFrame(this.animate)
    },
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
    createGos(list) {
      const animeGroup = {}
      this.initList = list.map((el, i) => {
        // 创建一个元素
        let item = {};
        // let anime = {}
        // 元素索引值 前6个固定 后面的从0开始
        let key = i - 6;
        // 获取当前元素在param里的索引值
        let index = key
        // key>=0 时 开始规律循环
        if (key >= 0) {
          index = Math.floor(key % 92 / 2)
        }
        // 当前该元素 类型 方向 left top 
        const { type, direction: _direction, x, y } = this.param[index]
        const { width, height } = wh[type]
        // 判断方向 中线上面0 下面1
        let direction = _direction;
        let left = x
        let top = 0
        const startX = Math.floor(Math.random() * this.mid * 2 - this.mid)
        const startY = Math.floor(Math.random() * this.mid * 2 - this.mid)
        const startZ = Math.floor(Math.random() * this.mid * 2 - this.mid)
        // 元素
        item = {
          key,
          width,
          height,
          left,
          top,
          transform: `matrix3d(1,0,0,0, 0,1,0,0, 0,0,1,0, ${startX}, ${startY}, ${startZ},1)`,
          // 图片源
          src: '',
        }
        // 设置 left的值
        if (key >= 0) {
          direction = key % 2 ? 0 : 1
          // 92个图片为1组 
          const group = Math.floor(key / 92)
          // left的值
          left = (direction == 0 ? wh[3].width * 2 : 0) + +x + (this.piece * group)
          // 设置left的值 和 中心点x的值
          item.left = left;
        }
        // 0上 1下 中线上面的还要剪掉自身高度
        top = direction === 0 ? this.mid - y - height : this.mid + y
        item.top = top;
        // 元素运动集合
        animeGroup[key] = {
          key,
          width,
          height,
          initX: left,
          initY: top,
          initOrigin: {
            x: left + width / 2,
            y: top + height / 2,
          },
          x: left,
          y: top,
          origin: {
            x: left + width / 2,
            y: top + height / 2,
          },
          transform: {
            x: startX,
            y: startY,
            z: startZ
          },
          move: 0
        }
        return item
      })
      this.animeGroup = animeGroup
    },
    // 开机动画
    initAnime(time = 600) {
      setTimeout(() => {
        const obj = {};
        Object.values(this.animeGroup).map((ele) => {
          const { key, transform } = ele
          const option = { ...transform, opacity: 0.3 }
          obj['e' + key] = new TWEEN.Tween(option, false) // 创建一个修改“坐标”的新 tween。
            .to({ x: 0, y: 0, z: 0, opacity: 1 }, 1200) // 在 1 秒内移动到 (300, 200)。
            .easing(TWEEN.Easing.Quadratic.In) // 使用缓动函数使动画流畅。
            .onUpdate(() => {
              this.$refs['box' + key][0]?.style.setProperty('transform', `matrix3d(1,0,0,0, 0,1,0,0, 0,0,1,0, ${option.x},${option.y},${option.z},1)`)
              this.$refs['box' + key][0]?.style.setProperty('opacity', option.opacity)
            })
            .delay(Math.floor(Math.random() * 600))
            .start()
          this.animeGroup[key] = {
            ...ele,
            transform: { x: 0, y: 0, z: 0 }
          }
        })
        function animate() {
          for (let el in obj) {
            obj[el].update()
          }
          requestAnimationFrame(animate)
        }
        requestAnimationFrame(animate)
      }, time);
    },
    // 获取2点间的距离和角度
    angle(start, end) {
      const diff_x = end.x - start.x;
      const diff_y = -(end.y - start.y);
      //返回角度,不是弧度
      const deg = 180 / Math.PI * Math.atan2(diff_y, diff_x)
      const radianS = Math.sin(deg * Math.PI / 180)
      const radianC = Math.cos(deg * Math.PI / 180)
      // 返回2点的距离
      const w = Math.abs(end.x - start.x)
      const h = Math.abs(end.y - start.y)
      const range = Math.sqrt(Math.pow(w, 2) + Math.pow(h, 2))
      return {
        range,
        deg,
        radianS,
        radianC,
      }
    },
    // 绑定事件
    initHandle() {
      let target = null
      let key = 0
      let move = false
      let initX = 0
      let initY = 0
      let subX = 0
      let subY = 0
      const imsg = document.querySelectorAll('.box-show')
      for (let img of imsg) {
        img.addEventListener('mousedown', (e) => {
          const { pageX, pageY } = e
          key = e.target.attributes['attr-key'].value
          target = this.$refs['show' + key][0]
          initX = pageX
          initY = pageY
          move = true
        })
      }
      document.addEventListener('mousemove', (e) => {
        if (move && target) {
          const showAnime = JSON.parse(JSON.stringify(this.showAnime))
          const showList = Object.values(showAnime)
          const { pageX, pageY } = e
          subX = pageX - initX
          subY = pageY - initY
          initX = pageX
          initY = pageY
          const anime = this.showAnime[key]
          const { x, y, origin: { x: ox, y: oy } } = anime
          const mx = x + subX
          const my = y + subY
          const _ox = ox + subX;
          const _oy = oy + subY;
          target.style.left = mx + 'px'
          target.style.top = my + 'px'
          showAnime[key] = {
            ...anime,
            x: mx,
            y: my,
            origin: {
              x: _ox,
              y: _oy,
            }
          }
          const { maxRange } = this.showOption;
          showList.filter(({ key: eKey }) => eKey != key).map(item => {
            console.log(item)
            const { x, y, origin, } = item
            const { range, radianS, radianC } = this.angle({ x: _ox, y: _oy }, origin)
            if (range < maxRange) {
              // 最大距离 - 当前移动元素中心点和周围元素中心点的距离
              const subRange = maxRange - range;
              // 移动x,y轴的距离
              const subX = Math.floor(radianC * subRange)
              const subY = -Math.floor(radianS * subRange)
              // x，y轴移动总距离
              const mx = x + subX
              const my = y + subY
              const current = this.$refs['show' + item.key][0]
              current.style.left = mx + 'px'
              current.style.top = my + 'px'
              showAnime[key] = {
                ...item,
                x: mx,
                y: my,
                origin: {
                  x: origin.x + subX,
                  y: origin.y + subY,
                },
              }
            }
          })
          this.showAnime = showAnime
        }
      })
      document.addEventListener('mouseup', () => {
        if (move || target) {
          target = null
          move = false
        }
      })
    },
    // 滚动事件
    initScroll() {
      const scrollEle = this.$refs.list;
      let initScrollLeft = 0
      scrollEle.addEventListener('scroll', (e) => {
        const { target: { scrollLeft } } = e;
        const subX = scrollLeft - initScrollLeft
        initScrollLeft = scrollLeft;
        const showAnime = JSON.parse(JSON.stringify(this.showAnime))
        const { width } = this.showOption
        Object.values(this.showAnime).map(ele => {
          const { key, x, origin: { y } } = ele
          const mx = x + subX
          showAnime[key] = {
            ...ele,
            x: mx,
            origin: {
              x: mx + width / 2,
              y
            }
          }
          this.$refs['show' + key][0].style.left = mx + 'px'
        })
        this.showAnime = showAnime
      })
    },
    // 点击图片
    handleClick(item) {
      const { key, left, top } = item;
      const showList = JSON.parse(JSON.stringify(this.showList))
      if (!this.showAnime[key]) {
        const len = Object.keys(this.showAnime).length
        const anime = this.animeGroup[key]
        const { width, height } = anime
        const { width: sWidth, height: sHeight } = this.showOption
        const sx = (sWidth - width) / 2, sy = (sHeight - height) / 2;
        showList[len] = {
          ...anime,
          width: sWidth,
          height: sHeight,
          left: left - sx,
          top: top - sy,
        }
        this.showAnime[key] = { ...anime, x: left - sx, y: top - sy }
        this.showList = showList;
      }
      this.$nextTick(() => {

      })
    },
    // 隐藏按钮
    handleHidden(item) {
      const { key } = item;
      const showList = JSON.parse(JSON.stringify(this.showList))
      const shows = showList.map(({ key }) => key)
      const index = shows.indexOf(key)
      showList[index] = {}
      this.showList = showList
      delete this.showAnime[key]
    },
    reset(item, data = []) {
      const { initOrigin } = item;
      const arr = new Array(data.length).fill(false)
      const { maxRange, speed } = this.animeOption
      data.map((item, index) => {
        const { range } = this.angle(item.origin, initOrigin)
        if (range > maxRange + speed) {
          arr[index] = true
        }
      })
      return arr.filter(item => item).length == data.length
    },
    animeUpload() {
      const show = Object.values(this.showAnime)
      // 更新移动元素数据
      const animeGroup = this.anime(show)
      const { speed } = this.animeOption
      Object.values(animeGroup).filter(({ move }) => !move).map(item => {
        const bool = this.reset(item, show)
        if (bool) {
          const {
            key,
            // 初始中心点
            initOrigin: { x: ix, y: iy },
            // 位置偏移量
            transform: { x: tx, y: ty },
          } = item;
          const mx = tx + (tx >= 0 ? -(tx / speed) : Math.ceil(-tx / speed))
          const my = ty + (ty >= 0 ? -(ty / speed) : Math.ceil(-ty / speed))
          animeGroup[key] = {
            ...item,
            origin: {
              x: ix + mx,
              y: iy + my
            },
            transform: {
              x: mx,
              y: my,
              z: 0
            },
            move: 2
          }
        }
      })
      // 对运动的元素 进行位移更新
      const data = Object.values(animeGroup)
      data.map(item => {
        const { key, transform: { x, y }, move } = item;
        let mx = x, my = y
        if (move) {
          this.$refs['box' + key][0].style.setProperty('transform', `matrix3d(1,0,0,0, 0,1,0,0, 0,0,1,0, ${mx},${my},0,1)`)
        }
        // 初始化移动状态
        animeGroup[key] = {
          ...item,
          move: false
        }
      })
      this.animeGroup = animeGroup;
      window.animeGroup = animeGroup
      window.showAnime = this.showAnime
      window.showList = this.showList
    },
    anime(show) {
      // 拷贝元素集数据
      const animeGroup = JSON.parse(JSON.stringify(this.animeGroup))
      const { maxRange, speed } = this.animeOption
      show.map(item => {
        // 当前移动元素及中心点位置
        const { origin: { x: sx, y: sy } } = item
        // 遍历元素集 更新数据
        Object.values(animeGroup).map((ele) => {
          const {
            key,
            // 初始中心点
            initOrigin: { x: ix, y: iy },
            // 当前中心点
            origin: { x: ex, y: ey },
            // 位置偏移量
            transform: { x: tx, y: ty },
          } = ele;
          const { range: initRange } = this.angle({ x: sx, y: sy }, { x: ix, y: iy })
          const { range, radianS, radianC } = this.angle({ x: sx, y: sy }, { x: ex, y: ey })
          // 移动到的位置
          let mx = 0;
          let my = 0;
          if (initRange < maxRange) {
            // 最大距离 - 当前移动元素中心点和周围元素中心点的距离
            const subRange = maxRange - range;
            // 移动x,y轴的距离
            const subX = radianC * subRange
            const subY = -(radianS * subRange)
            // x，y轴移动总距离
            mx = tx + (subX / speed)
            my = ty + (subY / speed)
            animeGroup[key] = {
              ...ele,
              origin: {
                x: ix + mx,
                y: iy + my
              },
              transform: {
                x: mx,
                y: my,
                z: 0
              },
              move: true
            }
          }
        })
      })
      return animeGroup;
    },
    randomRange(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min
    },
    bindStyle(item) {
      const { width, height, left, top, transform } = item;
      return {
        width: width + 'px',
        height: height + 'px',
        left: left + 'px',
        top: top + 'px',
        transform,
        padding: this.padding
      }
    },
    bindStyleShow(item) {
      const { width = 0, height = 0, left = 0, top = 0 } = item;
      return {
        width: width + 'px',
        height: height + 'px',
        left: left + 'px',
        top: top + 'px',
        padding: this.padding
      }
    }

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
  overflow-x: auto;

  &::-webkit-scrollbar {
    // display: none;
  }

  .box {
    position: absolute;
    transform-origin: center center;
    opacity: 0.3;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
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
  }

  .box-show {
    position: absolute;
    transform-origin: center center;
    opacity: 1;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    z-index: 29;

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


.line {
  position: fixed;
  top: 50%;
  left: 0;
  width: 100%;
  border-top: 1px red solid;
}
</style>
