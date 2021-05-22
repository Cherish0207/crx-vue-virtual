<template>
  <div class="viewport" ref="viewport" @scroll="scrollFn">
    <div class="scrollbar" ref="scrollbar"></div>
    <div
      class="scrolllist"
      ref="scrolllist"
      :style="{ transform: `translate3d(0,${offset}px, 0)` }"
    >
      <div v-for="item in visibleData" :key="item.id" :vid="item.id">
        <slot :item="item"></slot>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    size: Number,
    remain: Number,
    items: Array,
  },
  data() {
    return {
      start: 0,
      end: this.remain,
      offset: 0,
    };
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
    this.$refs.viewport.style.height = this.size * this.remain + 'px';
    this.$refs.scrollbar.style.height = this.size * this.items.length + 'px';
  },
  methods: {
    scrollFn() {
      const scrollTop = this.$refs.viewport.scrollTop;
      console.log(scrollTop);
      this.start = Math.floor(scrollTop / this.size);
      this.end = this.start + this.remain;
      this.offset = (this.start - this.prevCount) * this.size;
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