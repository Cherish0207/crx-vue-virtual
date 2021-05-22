<template>
  <div id="app">
    <pre>
      用户已经把数据都给我了
    虚拟列表 vue-virtual-scroll-list
      VirtualList: 只显示可视区域
      1.size 每条列表的高度 -- 为了算出滚动条
      2.remain 可视区域有多少条数据
      3.items 长列表的所有数据
    
    viewport 能滚动的盒子
    scrollbar 自己做的滚动条
    scrolllist 真实渲染的内容

    作用域插槽，就是在父组件中传参子组件 会调用去执行

    scroll事件 截流

    1.先算出来当前滚过去几个了/当前应该从第几个开始显示/获取当前应该从第几个开始渲染
2.当前可渲染的数据列表
3.定位当前的可视区域，让可视区域去调整偏移位置

预留渲染:不会有半个列表的情况，减少白屏,体验效果提升--可以让用户自己去配置预留渲染多少数据

如果不知道多高怎么做
    </pre>
    <VirtualList :size="40" :remain="8" :items="items">
      <item slot-scope="{ item }" :item="item"></item>
    </VirtualList>
  </div>
</template>

<script>
import VirtualList from './components/virtual-list.vue';
import item from './components/virtual-list-item';
const items = [];
for (let i = 0; i < 10000; i++) {
  items.push({ id: i, value: i });
}
export default {
  data() {
    return {
      items,
    };
  },
  components: {
    VirtualList,
    item,
  },
};
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
