<template>
  <div class="viewport" ref="viewport" @scroll="throttleFn">
    <div class="scrollbar" ref="scrollbar"></div>
    <div
      class="scrolllist"
      ref="scrolllist"
      :style="{ transform: `translate3d(0,${offset}px, 0)` }"
    >
      <div
        v-for="item in visibleData"
        :key="item.id"
        :vid="item.id"
        ref="items"
      >
        <slot :item="item"></slot>
      </div>
    </div>
  </div>
</template>
<script>
import throttle from "lodash/throttle";
export default {
  props: {
    size: Number,
    remain: Number,
    items: Array,
    variable: Boolean,
  },
  data() {
    return {
      start: 0,
      end: this.remain,
      offset: 0,
      position: [],
    };
  },
  created() {
    this.throttleFn = throttle(this.scrollFn, 200, { leading: false });
  },
  computed: {
    prevCount() {
      return Math.min(this.start, this.remain);
    },
    nextCount() {
      return Math.min(this.items.length - this.end, this.remain);
    },
    visibleData() {
      const start = this.start - this.prevCount;
      const end = this.end + this.nextCount;
      return this.items.slice(start, end);
    },
  },
  mounted() {
    this.$refs.viewport.style.height = this.size * this.remain + "px";
    this.$refs.scrollbar.style.height = this.size * this.items.length + "px";
    this.cacheList();
  },
  updated() {
    console.log("updated");
    this.$nextTick(() => {
      let nodes = this.$refs.items;
      if (!(nodes && nodes.length > 0)) return;
      nodes.forEach((node) => {
        debugger
        let { height } = node.getBoundingClientRect();
        let id = node.getAttribute("vid") - 0;
        let oldHeight = this.position[id].height;
        let val = oldHeight - height;
        if (val) {
          this.position[id].height = height;
          this.position[id].bottom -= val;
          for (let i = id + 1; i < this.position.length; i++) {
            this.position[i].top = this.position[i - 1].bottom;
            this.position[i].bottom -= val;
          }
        }
      });
      this.$refs.scrollbar.style.height =
        this.position[this.position.length - 1].bottom + "px";
    });
  },
  methods: {
    cacheList() {
      this.position = this.items.map((item, index) => ({
        top: this.size * index,
        bottom: this.size * (index + 1),
        height: this.size,
      }));
    },
    getStartIndex(value) {
      let start = 0,
        end = this.position.length - 1,
        temp = null;
      while (start < end) {
        let middleIndex = parseInt((start + end) / 2);
        let middleValue = this.position[middleIndex].bottom;
        if (middleValue === value) {
          return middleIndex + 1;
        } else if (middleValue < value) {
          start = middleIndex + 1;
        } else if (middleValue > value) {
          if (temp === null || temp > middleIndex) {
            temp = middleIndex;
          }
          end = middleIndex - 1;
        }
      }
      return temp;
    },
    scrollFn() {
      const scrollTop = this.$refs.viewport.scrollTop;
      // console.log('scroll',scrollTop);
      if (this.variable) {
        this.start = this.getStartIndex(scrollTop);
        this.offset = this.position[this.start - this.prevCount]
        ? this.position[this.start - this.prevCount].top
        : 0;
        // let start = this.start
        // let offset = this.offset
        // debugger
      } else {
        this.start = Math.floor(scrollTop / this.size);
        this.offset = (this.start - this.prevCount) * this.size;
      }
      this.end = this.start + this.remain;
    },
  },
};
</script>
<style>
.viewport {
  overflow: scroll;
  position: relative;
}
.scrolllist {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
}
</style>
