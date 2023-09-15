## 长列表渲染的解决思路,以及基于 vue 实现虚拟滚动列表
### 超长列表渲染性能优化
#### 分片渲染

通过浏览器事件循环机制，分割渲染时间。每次固定加载 n 条，递归渲染。如先渲染 50 个，渲染完毕后再循环渲染下一个 50。

原理：

定时器是宏任务，会等待ui渲染完成后执行。可以用 requestAnimationFrame 优化，都是宏任务，可以配合浏览器的刷新频率。

缺点：

页面 DOM 元素过多，造成页面卡顿。滚动的时候会有卡顿的问题

使用场景：
+ 滚动加载
+ 要实现勾选｜操作列表时，只能用分片，不能用虚拟列表
#### 虚拟列表vue-virtual-scroll-list  
- 属性
  - size 每条列表高度 -- 为了算出滚动条
  - remain 可视区域有多少条数据
  - items 长列表的所有数据
- 页面布局
   - viewport 视口
   - scrollbar 自己做的滚动条
   - scrolllist 真实渲染的区域
- 实现思路
   - data: start end offset
   - 页面加载后通过 size remain items 计算 viewport 和 scrollbar 的高度
   - 滚动时, 根据 scrollbar 的 scrollTop 计算 start offset 
   - --> transform:translate3d(0,offset,0) 定位当前的可视区域，让可视区域去调整偏移位置
   - --> visibleData:items.slice(start, end) 当前可渲染的数据列表
- 预留渲染
   - computed: prevCount nextCount
   - offset = (start - prevCount) * size
   - 不会有半个列表的情况，减少白屏,体验效果提升--(可以做成用户自定义的)
    事先不知道size
   - data: variable size(预计高度) position[]
   - 页面加载后根据 size 缓存每一项列表的 top bottom height 到 position
   - getStartIndex 根据 scrollTop 使用二分查找找到对应的start
   - 不会有半个列表的情况，减少白屏,体验效果提升--(可以做成用户自定义的)
- 优化点
   - throttle 截流 scroll 事件
   - 作用域插槽，在父组件中传参 子组件调用
- 使用场景
   - 用户已经把超长的大列表数据给我了