<script setup lang="ts">
import { IconTypeEnum } from '@/enums/appEnum'

const icon1 = ref('&#xe6b5;')
const icon2 = ref('iconsys-baitianmoshi3')
const icon3 = ref('iconsys-baitianmoshi3')
</script>

<template>
  <div class="page-content">
    <div class="select">
      <div class="item">
        <h3>Unicode</h3>
        <ArtIconSelector v-model="icon1" :icon-type="IconTypeEnum.UNICODE" />
      </div>
      <div class="item">
        <h3>ClassName</h3>
        <ArtIconSelector v-model="icon2" :icon-type="IconTypeEnum.CLASS_NAME" width="260px" />
      </div>
      <div class="item">
        <h3>禁用</h3>
        <ArtIconSelector
          v-model="icon3"
          :icon-type="IconTypeEnum.CLASS_NAME"
          width="260px"
          disabled
        />
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
  .select {
    .item {
      margin-bottom: 30px;

      h3 {
        padding-bottom: 10px;
        font-size: 16px;
        font-weight: 500;
      }
    }
  }
</style>
