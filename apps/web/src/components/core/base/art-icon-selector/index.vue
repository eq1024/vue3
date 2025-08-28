<!-- 图标选择器 -->
<script setup lang="ts">
import type { IconfontType } from '@/utils/constants'
import { IconTypeEnum } from '@/enums/appEnum'
import { extractIconClasses } from '@/utils/constants'

defineOptions({ name: 'ArtIconSelector' })

// 使用 withDefaults 定义 props
const props = withDefaults(defineProps<Props>(), {
  iconType: IconTypeEnum.CLASS_NAME,
  modelValue: '',
  text: '图标选择器',
  width: '200px',
  size: 'default',
  disabled: false,
})

// 定义 emits
const emits = defineEmits<Emits>()

  // 组件大小类型
  type ComponentSize = 'large' | 'default' | 'small'

// Props 接口定义
interface Props {
  /** 图标类型 */
  iconType?: IconTypeEnum
  /** v-model 绑定的图标值 */
  modelValue?: string
  /** 显示文本 */
  text?: string
  /** 组件宽度 */
  width?: string
  /** 组件大小 */
  size?: ComponentSize
  /** 是否禁用 */
  disabled?: boolean
}

// Emits 接口定义
interface Emits {
  'update:modelValue': [value: string]
  'getIcon': [value: string]
}

// 响应式数据
const selectValue = ref<string>(props.modelValue)
const visible = ref<boolean>(false)
const activeName = ref<string>('icons')

// 图标列表 - 使用计算属性优化性能
const iconsList = computed<IconfontType[]>(() => extractIconClasses())

// 监听 modelValue 变化
watch(
  () => props.modelValue,
  (newVal: string) => {
    selectValue.value = newVal
  },
  { immediate: true },
)

// 选择图标
function selectorIcon(icon: IconfontType): void {
  const iconValue
      = props.iconType === IconTypeEnum.CLASS_NAME ? icon.className : icon.unicode || ''

  selectValue.value = iconValue
  visible.value = false

  // 发射 v-model 更新事件和自定义事件
  emits('update:modelValue', iconValue)
  emits('getIcon', iconValue)
}

// 处理点击事件
function handleClick(): void {
  if (!props.disabled) {
    visible.value = true
  }
}

// 清除图标
function clearIcon(): void {
  selectValue.value = ''

  // 发射 v-model 更新事件和自定义事件
  emits('update:modelValue', '')
  emits('getIcon', '')
}

// 计算属性：当前图标类型（用于模板中的判断）
const iconType = computed<IconTypeEnum>(() => props.iconType)
</script>

<template>
  <div class="icon-selector">
    <div
      class="select"
      :style="{ width: props.width }"
      :class="[size, { 'is-disabled': disabled }, { 'has-icon': selectValue }]"
      @click="handleClick"
    >
      <div class="icon">
        <i
          v-show="props.iconType === IconTypeEnum.CLASS_NAME"
          :class="`iconfont-sys ${selectValue}`"
        />
        <i
          v-show="props.iconType === IconTypeEnum.UNICODE"
          class="iconfont-sys"
          v-html="selectValue"
        />
      </div>
      <div class="text">
        {{ props.text }}
      </div>
      <div class="arrow">
        <i class="iconfont-sys arrow-icon">&#xe709;</i>
        <i class="iconfont-sys clear-icon" @click.stop="clearIcon">&#xe83a;</i>
      </div>
    </div>

    <el-dialog v-model="visible" title="选择图标" width="40%" align-center>
      <el-scrollbar height="400px">
        <ul v-show="activeName === 'icons'" class="icons-list">
          <li v-for="icon in iconsList" :key="icon.className" @click="selectorIcon(icon)">
            <i
              v-show="iconType === IconTypeEnum.CLASS_NAME"
              :class="`iconfont-sys ${icon.className}`"
            />
            <i
              v-show="iconType === IconTypeEnum.UNICODE"
              class="iconfont-sys"
              v-html="icon.unicode"
            />
          </li>
        </ul>
      </el-scrollbar>

      <template #footer>
        <span class="dialog-footer">
          <el-button @click="visible = false">取 消</el-button>
          <el-button type="primary" @click="visible = false">确 定</el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<style lang="scss" scoped>
  .icon-selector {
    width: 100%;

    .select {
      box-sizing: border-box;
      display: flex;
      align-items: center;
      justify-content: space-between;
      height: var(--el-component-custom-height);
      padding: 0 15px;
      cursor: pointer;
      border: 1px solid var(--art-border-dashed-color);
      border-radius: calc(var(--custom-radius) / 3 + 2px) !important;
      transition: border 0.3s;

      @media (width <= 500px) {
        width: 100% !important;
      }

      &.large {
        height: 40px;
      }

      &.small {
        height: 24px;
      }

      &:hover:not(.is-disabled).has-icon {
        .arrow-icon {
          display: none;
        }

        .clear-icon {
          display: block !important;
        }
      }

      &:hover {
        border-color: var(--art-text-gray-400);
      }

      .icon {
        display: flex;
        align-items: center;
        width: 20px;
        color: var(--art-gray-700);

        i {
          display: block;
          margin: 0 auto;
          font-size: 16px;
        }
      }

      .text {
        display: flex;
        display: inline-block;
        align-items: center;
        width: 50%;
        font-size: 14px;
        color: var(--art-gray-600);

        @include ellipsis();

        @media (width <= 500px) {
          display: none;
        }
      }

      .arrow {
        display: flex;
        align-items: center;
        height: calc(100% - 2px);

        i {
          font-size: 13px;
          color: var(--art-gray-600);
        }

        .clear-icon {
          display: none;
        }
      }

      &.is-disabled {
        cursor: not-allowed;
        background-color: var(--el-disabled-bg-color);
        border-color: var(--el-border-color-lighter);

        .icon,
        .text,
        .arrow {
          color: var(--el-text-color-placeholder);
        }

        &:hover {
          border-color: var(--el-border-color-lighter);
        }
      }
    }

    .icons-list {
      display: grid;
      grid-template-columns: repeat(10, 1fr);
      border-top: 1px solid var(--art-border-color);
      border-left: 1px solid var(--art-border-color);

      li {
        box-sizing: border-box;
        display: flex;
        flex-direction: column;
        justify-content: center;
        aspect-ratio: 1 / 1;
        color: var(--art-gray-600);
        text-align: center;
        border-right: 1px solid var(--art-border-color);
        border-bottom: 1px solid var(--art-border-color);

        &:hover {
          cursor: pointer;
          background: var(--art-gray-100);
        }

        i {
          font-size: 22px;
          color: var(--art-gray-800);
        }
      }
    }
  }
</style>
