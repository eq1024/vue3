<!-- 更多按钮 -->
<script setup lang="ts">
import { useAuth } from '@/composables/useAuth'

defineOptions({ name: 'ArtButtonMore' })

const props = withDefaults(defineProps<Props>(), {
  hasBackground: true,
})

const emit = defineEmits<{
  (e: 'click', item: ButtonMoreItem): void
}>()

const { hasAuth } = useAuth()

export interface ButtonMoreItem {
  /** 按钮标识，可用于点击事件 */
  key: string | number
  /** 按钮文本 */
  label: string
  /** 是否禁用 */
  disabled?: boolean
  /** 权限标识 */
  auth?: string
}

interface Props {
  /** 下拉项列表 */
  list: ButtonMoreItem[]
  /** 整体权限控制 */
  auth?: string
  /** 是否显示背景 */
  hasBackground?: boolean
}

// 检查是否有任何有权限的 item
const hasAnyAuthItem = computed(() => {
  return props.list.some(item => !item.auth || hasAuth(item.auth))
})

function handleClick(item: ButtonMoreItem) {
  emit('click', item)
}
</script>

<template>
  <div class="btn-more">
    <ElDropdown v-if="hasAnyAuthItem">
      <ArtButtonTable type="more" :icon-bg-color="!hasBackground ? 'transparent' : ''" />
      <template #dropdown>
        <ElDropdownMenu>
          <template v-for="item in list" :key="item.key">
            <ElDropdownItem
              v-if="!item.auth || hasAuth(item.auth)"
              :disabled="item.disabled"
              @click="handleClick(item)"
            >
              {{ item.label }}
            </ElDropdownItem>
          </template>
        </ElDropdownMenu>
      </template>
    </ElDropdown>
  </div>
</template>
