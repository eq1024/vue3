<script setup lang="ts">
import imgUrl from '@imgs/state/404.png'

defineOptions({ name: 'Exception404' })
</script>

<template>
  <ArtException
    :data="{
      title: '404',
      desc: $t('exceptionPage.404'),
      btnText: $t('exceptionPage.gohome'),
      imgUrl,
    }"
  />
</template>
