<template>
  <div style="padding: 5px 10px; border: 1px solid skyblue; background: orange">
    {{ item.id }}===== {{ item.value }}
  </div>
</template>
<script>
export default {
  props: {
    item: Object,
  },
};
</script>
