<template>
  <div class="container">
    <ShowMoreText
      :mainText="testText"
      :maxRow="3"
      :isLimitHeight="isLimitHeight"
      v-model:isOver="isOver"
      @moreClick="isLimitHeight = !isLimitHeight"
    >
      <template v-slot:after>
        <span v-if="isOver" class="moreText">
          {{ moreText }}
          <el-icon v-if="isLimitHeight"><ArrowDown /></el-icon>
          <el-icon v-else><ArrowUp /></el-icon>
        </span>
      </template>
    </ShowMoreText>
  </div>
</template>

<script setup>
import ShowMoreText from '@/components/ShowMoreText.vue'

const isOver = ref(true)
const isLimitHeight = ref(true)
const testText =
  'Lorem ipsum dolor sit, amet consectetur adipisicing elit. Provident magni dolorem qui impedit sapiente voluptate illum, molestias similique quaerat officia unde quibusdam voluptatibus consequatur delectus assumenda ex, maiores dignissimos soluta explicabo libero facilis fugiat sunt tenetur? Officia aspernatur nulla exercitationem id, repellendus quas ipsum consequuntur harum, quia, aliquam illo laboriosam libero eos commodi earum praesentium! Ipsa consectetur repellendus explicabo, repudiandae nostrum modi, praesentium ut deserunt tenetur itaque suscipit, dolorum reprehenderit rem ad dolor perspiciatis natus blanditiis! Quia earum voluptatem voluptas eaque, eos minus vel tempora aut quidem molestias reiciendis cupiditate, illum quisquam hic sit corporis laboriosam. Vitae sed a cupiditate.'
const moreText = computed(() => (isLimitHeight.value ? '展开' : '收起'))
</script>

<style scoped lang="scss">
.container {
  width: 500px;
}

.moreText {
  cursor: pointer;
  color: #4899d0;
}
</style>
