<template>
  <!-- 置顶进度条 -->
  <base-top-progress-bar />
  <div class="container">
    <div class="btn_group">
      <!-- 主题切换 -->
      <el-button @click="toggleDark()">
        <el-icon>
          <component :is="icon"></component>
        </el-icon>
        <span>{{ isDark ? 'Dark' : 'Light' }}</span>
      </el-button>
      <!-- 主题切换结束 -->
      <el-button-group>
        <el-button type="primary" @click="toPrev">上一个</el-button>
        <el-button type="primary" @click="toNext">下一个</el-button>
      </el-button-group>
    </div>
    <div class="title">
      <h4>{{ curTitle }}</h4>
    </div>
    <div class="main">
      <keep-alive>
        <component :is="curComponent"></component>
      </keep-alive>
    </div>
  </div>
</template>
<script setup>

import BaseCount from '@/components/BaseCount.vue'
import ShowMoreText from '@/views/showMoreText/ShowMoreText.vue'
import BaseEchart from '@/views/baseEcharts/Index.vue'

const componentsList = [
  { title: '计数', com: BaseCount },
  { title: '文本展开折叠', com: ShowMoreText },
  { title: 'echarts', com: BaseEchart },
]

const curIndex = ref(0)
const curTitle = computed(() => componentsList[curIndex.value].title)
const curComponent = computed(() => componentsList[curIndex.value].com)
const toPrev = () => {
  curIndex.value > 0 && curIndex.value--
}
const toNext = () => {
  curIndex.value < componentsList.length - 1 && curIndex.value++
}

// 主题切换
const isDark = useDark({})
const toggleDark = useToggle(isDark)
const icon = computed(() => (isDark.value ? 'moon' : 'sunny'))
</script>

<style scoped lang="scss">
.container {
  height: 100%;
  display: flex;
  flex-direction: column;
  gap: 30px;
  align-items: center;

  .btn_group {
    display: flex;
    gap: 20px;
  }
}
</style>
