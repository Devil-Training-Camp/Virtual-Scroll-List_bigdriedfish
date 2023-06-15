<script setup>
import { onMounted, ref } from 'vue'
const props = defineProps({
  // 多加载的项数
  caches: {
    type: Number,
    default: 2,
    required: false
  },
  // 数据
  data: {
    type: Array,
    default: () => [],
    required: true
  },
  // 视口高度
  viewHeight: {
    type: Number,
    default: 320,
    required: false
  },
  // 固定高度值
  fixedSize: {
    type: Number,
    default: 50,
    required: false
  },
  // 是否是固定高度
  fixed: {
    type: Boolean,
    default: true,
    required: true
  },
  // 预估高度
  estimatedHeight: {
    type: Number,
    default: 40,
    required: false
  }
})
const scrollBoxRef = ref()
const scrollBarRef = ref()
const scrollListRef = ref()

const scrollTop = ref(0)
let visibleData = ref([])

onMounted(() => {
  scrollBarRef.value.style.height = props.data.length * props.fixedSize + 'px'
  updateFixedVisibleData(scrollTop.value)
})
const updateFixedVisibleData = (scrollTop) => {
  scrollTop = scrollTop || 0
  // 起始位置索引
  const startIndex = Math.floor(scrollTop / props.fixedSize)
  // 上缓冲区起始索引
  const topCacheIndex = Math.max(0, startIndex - props.caches)
  // 可视区可加载项数
  const visibleCount = Math.ceil(props.viewHeight / props.fixedSize)
  // 下缓冲区结束索引
  let endIndex = Math.min(props.data.length - 1, startIndex + visibleCount + 2)
  // 可视区值
  visibleData.value = props.data.slice(topCacheIndex, endIndex)
  // 当前展示的起始偏移
  scrollListRef.value.style.transform = `translateY(${startIndex * props.fixedSize}px)`
}

const handleScroll = () => {
  scrollTop.value = scrollBoxRef.value.scrollTop
  updateFixedVisibleData(scrollTop.value)
}
</script>
<template>
  <div ref="scrollBoxRef" class="scrollBox" @scroll="handleScroll">
    <div ref="scrollBarRef" class="scrollBar"></div>
    <div ref="scrollListRef" class="scrollList">
      <div v-for="item of visibleData" :key="item" class="scrollItem">
        {{ item }}
      </div>
    </div>
  </div>
</template>
<style scoped lang="css">
.scrollBox {
  position: relative;
  height: v-bind(viewHeight + 'px');
  overflow: auto;
  /* background-color: tomato; */
  margin: auto;
}
.scrollList {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: v-bind(viewHeight + 'px');
}
.scrollItem {
  height: v-bind(fixedSize + 'px');
}
</style>
