<template>
  <div class="overBox">
    <span ref="contentDom">{{ mainText }}</span>
    <span @click="handleMore" style="float: right">
      <slot name="after"></slot>
    </span>
  </div>
</template>

<script setup>
const { ctx } = getCurrentInstance()
const prop = defineProps({
  mainText: {
    type: String,
    default: '',
  },
  isLimitHeight: {
    type: Boolean,
    default: false,
  },
  maxRow: {
    type: Number,
    default: 1,
  },
})

const emit = defineEmits(['moreClick', 'update:isOver'])
const contentDom = ref()

onMounted(() => {
  init()
})

const init = () => {
  // 判断是否需要限制行数（高度）
  if (prop.isLimitHeight) {
    handleHeight()
  } else {
    contentDom.value.innerText = prop.mainText
  }
}

const handleMore = () => {
  emit('moreClick')
}

const handleHeight = () => {
  nextTick(() => {
    const contentBox = ctx.$el
    const moreDom = contentBox.querySelector('.moreText')
    // 设置成inline-block显示出来，获取它本身的高度
    moreDom.style.display = 'inline-block'
    // 获取限制maxRow最大行数的高度，以更多按钮的本身高度为参考物
    const maxHeight = prop.maxRow * moreDom.offsetHeight
    // 采用二分法进行判断需要显示多少内容
    let n = 999
    if (contentBox.offsetHeight > maxHeight) {
      let text = prop.mainText
      while (contentBox.offsetHeight > maxHeight && n > 0) {
        if (contentBox.offsetHeight > maxHeight * (prop.maxRow + 2)) {
          contentDom.value.innerText = text = text.substring(0, Math.floor(text.length / 2))
        } else {
          contentDom.value.innerText = text = text.substring(0, text.length - 1)
        }
        n--
      }
      // 需要展开时将末尾文字替换为...
      let finalText = contentDom.value.innerText.replace(/.$/, '...')
      contentDom.value.innerText = finalText
      emit('update:isOver', true)
    } else {
      emit('update:isOver', false)
    }
  })
}

watch(prop, () => {
  init()
})
</script>

<style scoped lang="scss">
.overBox {
  width: 100%;
  text-align: justify;
}
</style>
