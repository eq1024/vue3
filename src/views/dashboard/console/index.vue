<script setup lang="ts">
import { useCommon } from '@/composables/useCommon'
import AboutProject from './widget/AboutProject.vue'
import ActiveUser from './widget/ActiveUser.vue'
import CardList from './widget/CardList.vue'
import Dynamic from './widget/Dynamic.vue'
import NewUser from './widget/NewUser.vue'
import SalesOverview from './widget/SalesOverview.vue'
import TodoList from './widget/TodoList.vue'

defineOptions({ name: 'Console' })

useCommon().scrollToTop()
</script>

<template>
  <div class="console">
    <CardList />

    <el-row :gutter="20">
      <el-col :sm="24" :md="12" :lg="10">
        <ActiveUser />
      </el-col>
      <el-col :sm="24" :md="12" :lg="14">
        <SalesOverview />
      </el-col>
    </el-row>

    <el-row :gutter="20">
      <el-col :sm="24" :md="24" :lg="12">
        <NewUser />
      </el-col>
      <el-col :sm="24" :md="12" :lg="6">
        <Dynamic />
      </el-col>
      <el-col :sm="24" :md="12" :lg="6">
        <TodoList />
      </el-col>
    </el-row>

    <AboutProject />
  </div>
</template>

<style lang="scss" scoped>
  @use './style';
</style>
