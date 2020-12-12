<template>
  <div id="app">
    <div class="scroll-container" ref="scroller">
      <div class="scroll-wrapper">
        <div class="pulldown-animation-wrapper" v-if="pulldownStep > 0">
          <template
            v-if="pulldownStep <= 2"
          >
            <span
              v-show="currentPulldownInfo.icon"
              class="iconfont"
              :class="currentPulldownInfo.icon"
            ></span>
            <span class="message">{{ currentPulldownInfo.message }}</span>
          </template>

          <the-loading v-else-if="pulldownStep === 3" />

          <span v-else>刷新成功</span>
        </div>

        <ul class="list">
          <li
           class="item"
           v-for="item of mockDatas"
           :key="item.id"
          >
            <p>{{ item.id }} - {{ item.name }}</p>
          </li>
        </ul>
        <!-- 上拉加载更多时的 Loading -->
        <div class="pullup-animation-wrapper" v-if="isPullUp">
          <the-loading v-if="hasMoreData" />
          <span v-else>- 我也是有底线的 -</span>
        </div>
      </div>
      <header class="header"></header>
    </div>
  </div>
</template>

<script>
import BScroll from '@better-scroll/core';
import PullDown from '@better-scroll/pull-down';
import PullUp from '@better-scroll/pull-up';

import TheLoading from '@/components/TheLoading';

BScroll.use(PullDown);
BScroll.use(PullUp);

const sleep = (delay) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, delay);
  });
}

export default {
  components: {
    TheLoading
  },
  data () {
    return {
      mockDatas: [],
      isPulldown: false,
      // 下拉刷新配置
      pullDownRefresh: {
        threshold: 80, // 临界值
        stop: 50 // 下拉结束前回弹
      },
      pulldownStep: 0,
      pulldownStepInfo: [
        {
          step: 1,
          icon: 'icon-Arrowdown',
          message: '下拉刷新页面'
        },
        {
          step: 2,
          icon: 'icon-Arrowup',
          message: '松开即可刷新'
        }
      ],
      // 上拉加载配置
      pullUpLoad: {
        threshold: 0
      },
      isPullUp: false,
      hasMoreData: true, // 是否还有更多数据
      id: 1
    };
  },

  computed: {
    currentPulldownInfo () {
      const step = this.pulldownStep,
        pulldownStepInfo = this.pulldownStepInfo;

      return pulldownStepInfo.find((item) => item.step === step) || {};
    }
  },

  async mounted () {
    await this.getDatas();
    await this.$nextTick();

    this.initScroll();
  },

  methods: {
    getDatas (isRefresh) {
      return new Promise((resolve) => {
        if (isRefresh) {
          this.id = 1;
        }

        const list = [],
          id = this.id,
          len = id + 20;

        for (let i = id; i <= len; i ++) {
          list.push({
            id: i,
            name: 'This is a row.' + (Math.random() * 1000).toFixed(0)
          });
          this.id ++;
        }


        setTimeout(() => {
          if (isRefresh) {
            this.mockDatas = [];
          }
          if (this.id > 50) {
            resolve(false);
            return;
          }
          this.mockDatas.push(...list);
          resolve(true)
        }, 2000);
      });
    },

    initScroll () {
      const scroller = this.$refs.scroller,
        pullDownRefresh = this.pullDownRefresh,
        pullUpLoad = this.pullUpLoad;

      const bs = new BScroll(scroller, {
        click: true,
        probeType: 2,
        pullDownRefresh,
        pullUpLoad
      });

      // 滚动事件
      bs.on('scroll', (e) => {
        const { y } = e,
          { threshold, stop } = this.pullDownRefresh,
          isPulldown = this.isPulldown;

        // 下拉刷新步骤设置
        if (!isPulldown) {
          if (y >= threshold) {
            this.pulldownStep = 2
          } else if (y >= stop) {
            this.pulldownStep = 1;
          } else {
            this.pulldownStep = 0;
          }
        }
      });

      // 下拉刷新事件
      bs.on('pullingDown', async () => {
        this.isPulldown = true;
        this.pulldownStep = 3;

        await this.getDatas(true);
        this.$emit('pullingDown');
        this.pulldownStep = 4;

        await sleep(200);
        this.pulldownStep = 0;
        this.isPulldown = false;

        bs.finishPullDown();
        bs.refresh(); // 刷新scroller
      });

      // 上拉加载事件
      bs.on('pullingUp', async () => {
        console.log('已触底');
        this.isPullUp = true;
        await sleep(50);
        bs.refresh();
        const hasMoreData = await this.getDatas();

        if (!hasMoreData) {
          this.hasMoreData = false;
          await sleep(500);
        }
        this.isPullUp = false;

        bs.finishPullUp();
        bs.refresh();
        this.hasMoreData = true;
      });
    },
  }
};
</script>

<style lang="scss" scoped>
.scroll-container {
  height: calc(100% - 50px);
  margin-top: 50px;
  box-sizing: border-box;

  .scroll-wrapper {
    position: relative;
    min-height: 101%;
    background-color: #fff;
  }
}

.pulldown-animation-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  left: 0;
  top: -50px;
  width: 100%;
  height: 50px;
  background-color: #e1e1e1;
}

.pullup-animation-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
  color: #818181;
}

.header {
  position: fixed;
  left: 0;
  top: 0;
  z-index: 999;
  width: 100%;
  height: 50px;
  background-color: #0098ff;
}
</style>
