<script setup lang="ts">
import AppConfig from '@/config'
import { RoutesAlias } from '@/router/routesAlias'

defineOptions({ name: 'ForgetPassword' })

const router = useRouter()
const showInputLabel = ref(false)

const systemName = AppConfig.systemInfo.name
const username = ref('')
const loading = ref(false)

async function register() {}

function toLogin() {
  router.push(RoutesAlias.Login)
}
</script>

<template>
  <div class="login register">
    <LoginLeftView />
    <div class="right-wrap">
      <div class="header">
        <ArtLogo class="icon" />
        <h1>{{ systemName }}</h1>
      </div>
      <div class="login-wrap">
        <div class="form">
          <h3 class="title">
            {{ $t('forgetPassword.title') }}
          </h3>
          <p class="sub-title">
            {{ $t('forgetPassword.subTitle') }}
          </p>
          <div class="input-wrap">
            <span v-if="showInputLabel" class="input-label">账号</span>
            <ElInput v-model.trim="username" :placeholder="$t('forgetPassword.placeholder')" />
          </div>

          <div style="margin-top: 15px">
            <ElButton
              v-ripple
              class="login-btn"
              type="primary"
              :loading="loading"
              @click="register"
            >
              {{ $t('forgetPassword.submitBtnText') }}
            </ElButton>
          </div>

          <div style="margin-top: 15px">
            <ElButton class="back-btn" plain @click="toLogin">
              {{ $t('forgetPassword.backBtnText') }}
            </ElButton>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
  @use '../login/index';
</style>
