<script setup lang="ts">
import en from 'element-plus/es/locale/lang/en'
import zh from 'element-plus/es/locale/lang/zh-cn'
import { UserService } from './api/usersApi'
import { useUserStore } from './store/modules/user'
import { checkStorageCompatibility } from './utils/storage'
import { systemUpgrade } from './utils/sys'
import { setThemeTransitionClass } from './utils/theme/animation'

const userStore = useUserStore()
const { language } = storeToRefs(userStore)

const locales = {
  zh,
  en,
}

onBeforeMount(() => {
  setThemeTransitionClass(true)
})

onMounted(() => {
  // 检查存储兼容性
  checkStorageCompatibility()
  // 提升暗黑主题下页面刷新视觉体验
  setThemeTransitionClass(false)
  // 系统升级
  systemUpgrade()
  // 获取用户信息
  getUserInfo()
})

// 获取用户信息
async function getUserInfo() {
  if (userStore.isLogin) {
    try {
      const data = await UserService.getUserInfo()
      userStore.setUserInfo(data)
    }
    catch (error) {
      console.error('获取用户信息失败', error)
    }
  }
}
</script>

<template>
  <ElConfigProvider size="default" :locale="locales[language]" :z-index="3000">
    <RouterView />
  </ElConfigProvider>
</template>
