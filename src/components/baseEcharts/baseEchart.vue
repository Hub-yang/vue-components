<script setup lang="ts">
import { Ref } from 'vue'
import echarts, { type ECOption } from './config'
import { type EChartsType } from 'echarts/core'
import 'echarts-gl'

type EventType = (...args: any[]) => any
interface Props {
  option: ECOption
  width: string // 必须指定容器的宽高，否则无法显示。（容器内图表会自动获取父元素宽高）
  height: string
  theme?: Object | string
  loading?: boolean // 加载状态
  onMouseover?: EventType
  onMouseout?: EventType
}

const props = withDefaults(defineProps<Props>(), {
  theme: () => ({}),
  loading: false,
})

const chartRef = ref<Ref<HTMLDivElement>>()
const chartInstance = ref<EChartsType>()

const draw = () => {
  if (chartInstance.value) {
    chartInstance.value.setOption(props.option, { notMerge: true })
  }
}

const init = () => {
  if (!chartRef.value) return false

  // 确保 ECharts 实例单例
  chartInstance.value = echarts.getInstanceByDom(chartRef.value as any)
  if (!chartInstance.value) {
    chartInstance.value = markRaw(
      echarts.init(chartRef.value as any, props.theme, {
        renderer: 'canvas'
      })
    )

    // 绑定鼠标事件：
    if (props.onMouseover) {
      chartInstance.value.on('mouseover', (event: Object) => {
        (props.onMouseover as EventType)(event, chartInstance.value, props.option)
      })
    }
    if (props.onMouseout) {
      chartInstance.value.on('mouseout', (event: Object) => {
        (props.onMouseover as EventType)(event, chartInstance.value, props.option)
      })
    }

    draw()
  }
}

// 窗口自适应并开启过渡动画
const resize = () => {
  if (chartInstance.value) {
    chartInstance.value.resize({ animation: { duration: 300 } })
  }
}

// resize防抖
const debouncedResize = useDebounceFn(resize, 500, { maxWait: 800 })

// 对父组件暴露获取 ECharts 实例的方法，可直接通过实例调用原生函数
defineExpose({
  getInstance: () => chartInstance.value,
  resize,
  draw
})

watch(props, () => {
  draw()
})

// 监听loading
watch(
  () => props.loading,
  (loading) => {
    nextTick(() => {
      if (!chartInstance.value) return false
      loading ? chartInstance.value!.showLoading() : chartInstance.value!.hideLoading()
    })
  },
  {
    immediate: true
  }
)

onMounted(() => {
  init()
  window.addEventListener('resize', debouncedResize)
})

onBeforeUnmount(() => {
  // 容器被销毁之后，销毁实例，避免内存泄漏
  chartInstance.value?.dispose()
  window.removeEventListener('resize', debouncedResize)
})
</script>

<template>
  <div id="echart" ref="chartRef" :style="{ width: props.width, height: props.height }" />
</template>
