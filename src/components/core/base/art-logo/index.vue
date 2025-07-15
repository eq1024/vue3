<!-- 系统logo -->
<script setup lang="ts">
defineOptions({ name: 'ArtLogo' })

const props = withDefaults(defineProps<Props>(), {
  size: 36,
})

interface Props {
  /** logo 大小 */
  size?: number | string
}

const logoStyle = computed(() => ({ width: `${props.size}px` }))
</script>

<template>
  <div class="art-logo">
    <img :style="logoStyle" src="@imgs/common/logo.webp" alt="logo">
  </div>
</template>

<style lang="scss" scoped>
  .art-logo {
    display: flex;
    align-items: center;
    justify-content: center;

    img {
      width: 100%;
      height: 100%;
    }
  }
</style>
