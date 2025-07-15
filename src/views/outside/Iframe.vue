<script setup lang="ts">
import { getIframeRoutes } from '@/router/utils/menuToRouter'

const route = useRoute()
const isLoading = ref(true)
const iframeUrl = ref('')
const iframeRef = ref<HTMLIFrameElement | null>(null)

onMounted(() => {
  const iframeRoute = getIframeRoutes().find((item: any) => item.path === route.path)

  if (iframeRoute?.meta) {
    iframeUrl.value = iframeRoute.meta.link || ''
  }
})

function handleIframeLoad() {
  isLoading.value = false
}
</script>

<template>
  <div v-loading="isLoading" class="iframe-container">
    <iframe
      ref="iframeRef"
      :src="iframeUrl"
      frameborder="0"
      class="iframe-content"
      @load="handleIframeLoad"
    />
  </div>
</template>

<style scoped>
  .iframe-container {
    box-sizing: border-box;
    width: 100%;
    height: 100%;
  }

  .iframe-content {
    width: 100%;
    height: 100%;
    min-height: calc(100vh - 120px);
    border: none;
  }
</style>
