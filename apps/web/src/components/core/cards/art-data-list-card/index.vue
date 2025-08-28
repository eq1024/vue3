<!-- 数据列表卡片 -->
<script setup lang="ts">
defineOptions({ name: 'ArtDataListCard' })

const props = withDefaults(defineProps<Props>(), {
  maxCount: DEFAULT_MAX_COUNT,
})

const emit = defineEmits<{
  /** 点击更多按钮事件 */
  (e: 'more'): void
}>()

interface Props {
  /** 数据列表 */
  list: Activity[]
  /** 标题 */
  title: string
  /** 副标题 */
  subtitle?: string
  /** 最大显示数量 */
  maxCount?: number
  /** 是否显示更多按钮 */
  showMoreButton?: boolean
}

interface Activity {
  /** 标题 */
  title: string
  /** 状态 */
  status: string
  /** 时间 */
  time: string
  /** 样式类名 */
  class: string
  /** 图标 */
  icon: string
}

const ITEM_HEIGHT = 66
const DEFAULT_MAX_COUNT = 5

const maxHeight = computed(() => `${ITEM_HEIGHT * props.maxCount}px`)

const handleMore = () => emit('more')
</script>

<template>
  <div class="basic-list-card">
    <div class="art-card art-custom-card">
      <div class="card-header">
        <p class="card-title">
          {{ title }}
        </p>
        <p class="card-subtitle">
          {{ subtitle }}
        </p>
      </div>
      <ElScrollbar :style="{ height: maxHeight }">
        <div v-for="(item, index) in list" :key="index" class="list-item">
          <div v-if="item.icon" class="item-icon" :class="item.class">
            <i class="iconfont-sys" v-html="item.icon" />
          </div>
          <div class="item-content">
            <div class="item-title">
              {{ item.title }}
            </div>
            <div class="item-status">
              {{ item.status }}
            </div>
          </div>
          <div class="item-time">
            {{ item.time }}
          </div>
        </div>
      </ElScrollbar>
      <ElButton v-if="showMoreButton" v-ripple class="more-btn" @click="handleMore">
        查看更多
      </ElButton>
    </div>
  </div>
</template>

<style lang="scss" scoped>
  .basic-list-card {
    .art-card {
      padding: 30px;
      background-color: var(--art-main-bg-color);
      border-radius: var(--custom-radius);

      .card-header {
        padding-bottom: 15px;

        .card-title {
          font-size: 18px;
          font-weight: 500;
          color: var(--art-gray-900);
        }

        .card-subtitle {
          font-size: 14px;
          color: var(--art-gray-500);
        }
      }
    }

    .list-item {
      display: flex;
      align-items: center;
      padding: 12px 0;

      .item-icon {
        display: flex;
        align-items: center;
        justify-content: center;
        width: 40px;
        height: 40px;
        margin-right: 12px;
        border-radius: 8px;

        i {
          font-size: 20px;
        }
      }

      .item-content {
        flex: 1;

        .item-title {
          margin-bottom: 4px;
          font-size: 15px;
          color: var(--art-gray-900);
        }

        .item-status {
          font-size: 12px;
          color: var(--art-gray-600);
        }
      }

      .item-time {
        margin-left: 12px;
        font-size: 12px;
        color: var(--art-gray-500);
      }
    }

    .more-btn {
      width: 100%;
      margin-top: 25px;
      text-align: center;
    }
  }
</style>
