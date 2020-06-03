<template>
  <div class="container-style bg-f8 m-h-100">
    <ul class="waterfall-flow-ul" ref="waterfall">
      <li class="waterfall-flow-li" v-for="(item, index) in imgList" :key="index" :style="{'left': item.left + 'px', 'top': item.top + 'px', width: colWidth + 'px'}" @mouseenter="onMouseenter(index)" @mouseleave="onMouseleave">
        <div class="tip" v-if="mouseIndex === index">{{ item.describe }}</div>
        <img :src="item.url" alt="">
      </li>
    </ul>
  </div>
</template>

<script>
  import { cloneObj } from "../../utils/public-method";

  export default {
    name: "life-photo",
    data() {
      return {
        colNumbers: 4,
        colWidth: 460,
        colHeight: [],
        imgList: [
          { url: require('../../assets/images/1.jpg'),  describe: '这是美好的一天这是美好的一天这是美好的一天这是美好的一天这是美好的一天这是美好的一天这是美好的一天这是美好的一天'},
          { url: require('../../assets/images/2.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/10.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/3.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/4.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/5.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/9.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/6.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/7.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/8.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/11.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/5.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/6.jpg'),  describe: '这是美好的一天'},
          { url: require('../../assets/images/7.jpg'),  describe: '这是美好的一天'}
        ],
        newImgList: [],
        loading: true,
        mouseIndex: -1,
        resizeTimer: null
      }
    },
    methods: {
      // 计算宽度
      countColWidth() {
        let bodyW = document.body.clientWidth;
        if(bodyW > 1440) {
          this.colWidth = 460
        }else if(bodyW > 1200) {
          this.colWidth = 400
        }else if(bodyW > 992) {
          this.colWidth = 340
        }else {
          this.colWidth = 340
        }
        this.loadImage();
      },
      // 监听函数
      resizeEvent() {
        // 变化后需要做的事
        let that = this;
        clearTimeout(this.resizeTimer);
        this.resizeTimer = setTimeout(() => {
          that.countColWidth();
        }, 400)
      },
      // li鼠标进入
      onMouseenter(index) {
        this.mouseIndex = index;
      },
      onMouseleave() {
        this.mouseIndex = -1;
      },
      // 计算列数
      getColNumbers() {
        let clientWidth = this.$refs.waterfall.clientWidth;
        this.colNumbers = Math.floor(clientWidth / this.colWidth);
      },
      //读取图片
      loadImage() {
        this.loading = true;
        this.getColNumbers();
        let promise = Promise.resolve()
        this.imgList.forEach((ev, index) => {
          promise = promise.then(() => {
            return this.render(ev, index)
          })
        })
        promise.then(() => {
          this.loading = false
        })
      },
      render(ev, index) {
        return new Promise((resolve, reject) => {
          let image = new Image()
          let url = ev.url;
          image.src = url;
          image.onload = () => {
            let imgInfo = {
              url,
              ratio: image.width / image.height
            }
            let colIndex = index % this.colNumbers
            imgInfo.left = colIndex * this.colWidth;
            //首行 top为 0，记录每列的高度
            if (index < this.colNumbers) {
              imgInfo.top = 0;
              this.colHeight[colIndex] =  parseInt(this.colWidth / imgInfo.ratio)
            } else {
              //获取高度的最小值
              let minHeight = Math.min.apply(null, this.colHeight)
              let minIndex = this.colHeight.indexOf(minHeight)
              //此图片的 top 为上面图片的高度，left 相等
              imgInfo.top = minHeight
              imgInfo.left = minIndex * this.colWidth
              //把高度加上去
              this.colHeight[minIndex] += this.colWidth / imgInfo.ratio
            }
            // 赋值描述
            imgInfo.describe = this.imgList[index].describe;
            this.$set(this.imgList, index, imgInfo);
            resolve()
          }
          image.onerror = function (err) {
            reject(err)
            console.log('图片加载报错', err)
          }
        })
      }
    },
    created() {

    },
    destroyed() {
      window.removeEventListener('resize', this.resizeEvent);
    },
    mounted() {
      this.countColWidth();
      window.addEventListener('resize', this.resizeEvent)
    }
  }
</script>

<style scoped lang="scss">
  @import "./life-photo";
</style>