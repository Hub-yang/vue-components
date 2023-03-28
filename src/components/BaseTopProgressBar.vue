<template>
  <div class="scroll-progress" :style="{ width: progressWidth + '%' }"></div>
</template>

<script setup lang="ts">
export interface Props {
  bgColor?: string
}

const props = withDefaults(defineProps<Props>(), {
  bgColor: '#4CAF50',
})

const progressPer = ref<number>(0)

onMounted(() => {
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

const handleScroll = () => {
  const scrollTop = document.documentElement.scrollTop || document.body.scrollTop
  const scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight
  const clientHeight = document.documentElement.clientHeight || document.body.clientHeight
  const percent = (scrollTop / (scrollHeight - clientHeight)) * 100
  progressPer.value = percent
}

const progressWidth = computed<number>(() => {
  return Math.min(progressPer.value, 100)
})
</script>

<style lang='scss' scoped>
.scroll-progress {
  height: 4px;
  position: fixed;
  top: 0;
  left: 0;
  z-index: 999;
  transition: width 0.3s;
  background-color: v-bind("props.bgColor");
}
</style>