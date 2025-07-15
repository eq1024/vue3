<script setup lang="ts">
import lockImg from '@imgs/lock/lock_screen_1.webp'

const imageUrl = ref(lockImg)

function handleError(error: any) {
  console.error('裁剪错误:', error)
}

function handleLoadComplete(result: any) {
  console.log('图片加载完成:', result)
}

function handleLoadError(error: any) {
  console.error('图片加载失败:', error)
}
</script>

<template>
  <div class="page-content">
    <ArtCutterImg
      v-model:img-url="imageUrl"
      :box-width="540"
      :box-height="300"
      :cut-width="360"
      :cut-height="200"
      :quality="1"
      :tool="true"
      watermark-text="My Watermark"
      watermark-color="#ff0000"
      :show-preview="true"
      :original-graph="false"
      title="图片裁剪"
      preview-title="预览效果"
      @error="handleError"
      @image-load-complete="handleLoadComplete"
      @image-load-error="handleLoadError"
    />
  </div>
</template>
