<template>
  <div id="app">
    <VirtualList :size="40" :remain="8" :items="items" :variable="true">
      <item slot-scope="{ item }" :item="item"></item>
    </VirtualList>
  </div>
</template>

<script>
import VirtualList from './components/virtual-list.vue';
import item from './components/virtual-list-item';
import Mock from 'mockjs';
const items = [];
for (let i = 0; i < 10000; i++) {
  items.push({ id: i, value: Mock.Random.sentence(5, 50) });
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
