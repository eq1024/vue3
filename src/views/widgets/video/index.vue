<script setup lang="ts">
import lockImg from '@imgs/lock/lock_screen_1.webp'
import { ref } from 'vue'

// 视频源和封面图片URL
const videoUrl = ref(
  '//lf3-static.bytednsdoc.com/obj/eden-cn/nupenuvpxnuvo/xgplayer_doc/xgplayer-demo.mp4',
)
const posterUrl = ref(lockImg)
</script>

<template>
  <div class="page-content">
    <div class="video-container">
      <ArtVideoPlayer
        player-id="my-video-1"
        :video-url="videoUrl"
        :poster-url="posterUrl"
        :autoplay="false"
        :volume="1"
        :playback-rates="[0.5, 1, 1.5, 2]"
      />
    </div>
  </div>
</template>

<style scoped>
  .video-container {
    max-width: 600px;
  }
</style>
