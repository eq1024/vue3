<script setup lang="ts">
import type { FormInstance, FormRules } from 'element-plus'
import type { LanguageEnum } from '@/enums/appEnum'
import { ElMessage, ElNotification } from 'element-plus'
import { useI18n } from 'vue-i18n'
import { UserService } from '@/api/usersApi'
// 切换主题
import { useTheme } from '@/composables/useTheme'
import AppConfig from '@/config'
import { SystemThemeEnum } from '@/enums/appEnum'
import { languageOptions } from '@/locales'
import { RoutesAlias } from '@/router/routesAlias'
import { useSettingStore } from '@/store/modules/setting'
import { useUserStore } from '@/store/modules/user'

import { HttpError } from '@/utils/http/error'
import { getCssVar } from '@/utils/ui'

defineOptions({ name: 'Login' })

const { t } = useI18n()

  type AccountKey = 'super' | 'admin' | 'user'

export interface Account {
  key: AccountKey
  label: string
  userName: string
  password: string
  roles: string[]
}

const accounts = computed<Account[]>(() => [
  {
    key: 'super',
    label: t('login.roles.super'),
    userName: 'Super',
    password: '123456',
    roles: ['R_SUPER'],
  },
  {
    key: 'admin',
    label: t('login.roles.admin'),
    userName: 'Admin',
    password: '123456',
    roles: ['R_ADMIN'],
  },
  {
    key: 'user',
    label: t('login.roles.user'),
    userName: 'User',
    password: '123456',
    roles: ['R_USER'],
  },
])

const settingStore = useSettingStore()
const { isDark, systemThemeType } = storeToRefs(settingStore)

const dragVerify = ref()

const userStore = useUserStore()
const router = useRouter()
const isPassing = ref(false)
const isClickPass = ref(false)

const systemName = AppConfig.systemInfo.name
const formRef = ref<FormInstance>()

const formData = reactive({
  account: '',
  username: '',
  password: '',
  rememberPassword: true,
})

const rules = computed<FormRules>(() => ({
  username: [{ required: true, message: t('login.placeholder[0]'), trigger: 'blur' }],
  password: [{ required: true, message: t('login.placeholder[1]'), trigger: 'blur' }],
}))

const loading = ref(false)

onMounted(() => {
  setupAccount('super')
})

// 设置账号
function setupAccount(key: AccountKey) {
  const selectedAccount = accounts.value.find((account: Account) => account.key === key)
  formData.account = key
  formData.username = selectedAccount?.userName ?? ''
  formData.password = selectedAccount?.password ?? ''
}

// 登录
async function handleSubmit() {
  if (!formRef.value)
    return

  try {
    // 表单验证
    const valid = await formRef.value.validate()
    if (!valid)
      return

    // 拖拽验证
    if (!isPassing.value) {
      isClickPass.value = true
      return
    }

    loading.value = true

    // 登录请求
    const { username, password } = formData

    const { token, refreshToken } = await UserService.login({
      userName: username,
      password,
    })

    // 验证token
    if (!token) {
      throw new Error('Login failed - no token received')
    }

    // 存储token和用户信息
    userStore.setToken(token, refreshToken)
    const userInfo = await UserService.getUserInfo()
    userStore.setUserInfo(userInfo)
    userStore.setLoginStatus(true)

    // 登录成功处理
    showLoginSuccessNotice()
    router.push('/')
  }
  catch (error) {
    // 处理 HttpError
    if (error instanceof HttpError) {
      // console.log(error.code)
    }
    else {
      // 处理非 HttpError
      ElMessage.error('登录失败，请稍后重试')
      console.error('[Login] Unexpected error:', error)
    }
  }
  finally {
    loading.value = false
    resetDragVerify()
  }
}

// 重置拖拽验证
function resetDragVerify() {
  dragVerify.value.reset()
}

// 登录成功提示
function showLoginSuccessNotice() {
  setTimeout(() => {
    ElNotification({
      title: t('login.success.title'),
      type: 'success',
      duration: 2500,
      zIndex: 10000,
      message: `${t('login.success.message')}, ${systemName}!`,
    })
  }, 150)
}

// 切换语言
const { locale } = useI18n()

function changeLanguage(lang: LanguageEnum) {
  if (locale.value === lang)
    return
  locale.value = lang
  userStore.setLanguage(lang)
}

function toggleTheme() {
  const { LIGHT, DARK } = SystemThemeEnum
  useTheme().switchThemeStyles(systemThemeType.value === LIGHT ? DARK : LIGHT)
}
</script>

<template>
  <div class="login">
    <LoginLeftView />

    <div class="right-wrap">
      <div class="top-right-wrap">
        <div class="btn theme-btn" @click="toggleTheme">
          <i class="iconfont-sys">
            {{ isDark ? '&#xe6b5;' : '&#xe725;' }}
          </i>
        </div>
        <ElDropdown popper-class="langDropDownStyle" @command="changeLanguage">
          <div class="btn language-btn">
            <i class="iconfont-sys icon-language">&#xe611;</i>
          </div>
          <template #dropdown>
            <ElDropdownMenu>
              <div v-for="lang in languageOptions" :key="lang.value" class="lang-btn-item">
                <ElDropdownItem
                  :command="lang.value"
                  :class="{ 'is-selected': locale === lang.value }"
                >
                  <span class="menu-txt">{{ lang.label }}</span>
                  <i v-if="locale === lang.value" class="iconfont-sys icon-check">&#xe621;</i>
                </ElDropdownItem>
              </div>
            </ElDropdownMenu>
          </template>
        </ElDropdown>
      </div>
      <div class="header">
        <ArtLogo class="icon" />
        <h1>{{ systemName }}</h1>
      </div>
      <div class="login-wrap">
        <div class="form">
          <h3 class="title">
            {{ $t('login.title') }}
          </h3>
          <p class="sub-title">
            {{ $t('login.subTitle') }}
          </p>
          <ElForm
            ref="formRef"
            :model="formData"
            :rules="rules"
            style="margin-top: 25px"
            @keyup.enter="handleSubmit"
          >
            <ElFormItem prop="account">
              <ElSelect v-model="formData.account" class="account-select" @change="setupAccount">
                <ElOption
                  v-for="account in accounts"
                  :key="account.key"
                  :label="account.label"
                  :value="account.key"
                >
                  <span>{{ account.label }}</span>
                </ElOption>
              </ElSelect>
            </ElFormItem>
            <ElFormItem prop="username">
              <ElInput v-model.trim="formData.username" :placeholder="$t('login.placeholder[0]')" />
            </ElFormItem>
            <ElFormItem prop="password">
              <ElInput
                v-model.trim="formData.password"
                :placeholder="$t('login.placeholder[1]')"
                type="password"
                radius="8px"
                autocomplete="off"
                show-password
              />
            </ElFormItem>
            <div class="drag-verify">
              <div class="drag-verify-content" :class="{ error: !isPassing && isClickPass }">
                <ArtDragVerify
                  ref="dragVerify"
                  v-model:value="isPassing"
                  :text="$t('login.sliderText')"
                  text-color="var(--art-gray-800)"
                  :success-text="$t('login.sliderSuccessText')"
                  :progress-bar-bg="getCssVar('--el-color-primary')"
                  background="var(--art-gray-200)"
                  handler-bg="var(--art-main-bg-color)"
                />
              </div>
              <p class="error-text" :class="{ 'show-error-text': !isPassing && isClickPass }">
                {{
                  $t('login.placeholder[2]')
                }}
              </p>
            </div>

            <div class="forget-password">
              <ElCheckbox v-model="formData.rememberPassword">
                {{
                  $t('login.rememberPwd')
                }}
              </ElCheckbox>
              <RouterLink :to="RoutesAlias.ForgetPassword">
                {{ $t('login.forgetPwd') }}
              </RouterLink>
            </div>

            <div style="margin-top: 30px">
              <ElButton
                v-ripple
                class="login-btn"
                type="primary"
                :loading="loading"
                @click="handleSubmit"
              >
                {{ $t('login.btnText') }}
              </ElButton>
            </div>

            <div class="footer">
              <p>
                {{ $t('login.noAccount') }}
                <RouterLink :to="RoutesAlias.Register">
                  {{ $t('login.register') }}
                </RouterLink>
              </p>
            </div>
          </ElForm>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
  @use './index';
</style>
