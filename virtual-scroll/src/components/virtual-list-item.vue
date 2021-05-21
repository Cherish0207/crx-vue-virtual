<template>
  <div style="height: 40px; border: 1px solid skyblue;background:orange">{{ item.value }}</div>
</template>
<script>
export default {
  props: {
    item: Object,
  },
};
</script>
