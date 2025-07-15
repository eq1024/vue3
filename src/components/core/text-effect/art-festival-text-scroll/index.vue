<!-- 节日文本滚动 -->
<script setup lang="ts">
import { useCeremony } from '@/composables/useCeremony'
import { useSettingStore } from '@/store/modules/setting'

defineOptions({ name: 'ArtFestivalTextScroll' })

const settingStore = useSettingStore()
const { showFestivalText } = storeToRefs(settingStore)
const { currentFestivalData } = useCeremony()

function handleClose() {
  settingStore.setShowFestivalText(false)
}
</script>

<template>
  <div
    class="festival-text-scroll"
    :style="{
      height: showFestivalText ? '48px' : '0',
    }"
  >
    <ArtTextScroll
      v-if="showFestivalText && currentFestivalData?.scrollText !== ''"
      :text="currentFestivalData?.scrollText || ''"
      style="margin-bottom: 12px"
      show-close
      typewriter
      :speed="100"
      :typewriter-speed="150"
      @close="handleClose"
    />
  </div>
</template>

<style lang="scss" scoped>
  .festival-text-scroll {
    overflow: hidden;
    transition: height 0.5s ease-in-out;
  }
</style>
