<template>
  <div class="sticky top-0 z-50">
    <nav class="bg-white lg:pt-4">
      <div class="mx-auto transition-all duration-300 ease-in-out" :class="{'max-w-4xl': layout === 'list', 'max-w-6xl': layout === 'grid'}">
        <!-- 导航栏主要内容 -->
        <div class="flex items-center justify-between px-2 py-2">
          <!-- 左侧图标 -->
          <div class="flex items-center space-x-6">
            <!-- 实时更新按钮 -->
            <button
              @click="handleUpdate"
              :disabled="isUpdating"
              class="flex sm:flex-col items-center text-gray-700 hover:text-[#fb7299] transition-colors duration-200"
              :class="{
                'text-gray-500': isUpdating
              }"
              :title="syncDeleted ? '当前模式：同步已删除记录' : '当前模式：不同步已删除记录'"
            >
              <!-- 实时更新图标 - 加载中 -->
              <svg
                v-if="isUpdating"
                class="animate-spin w-5 h-5 sm:w-6 sm:h-6"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
              >
                <circle
                  class="opacity-25"
                  cx="12"
                  cy="12"
                  r="10"
                  stroke="currentColor"
                  stroke-width="4"
                ></circle>
                <path
                  class="opacity-75"
                  fill="currentColor"
                  d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
                ></path>
              </svg>

              <!-- 实时更新图标 - 正常模式 -->
              <svg
                v-if="!isUpdating && !syncDeleted"
                class="w-5 h-5 sm:w-6 sm:h-6"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
              >
                <path
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                ></path>
              </svg>

              <!-- 实时更新图标 - 同步已删除记录模式 (带垃圾桶图标) -->
              <svg
                v-if="!isUpdating && syncDeleted"
                class="w-5 h-5 sm:w-6 sm:h-6"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 24 24"
              >
                <!-- 刷新图标 -->
                <path
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                ></path>

                <!-- 垃圾桶图标 -->
                <g transform="translate(12, 12) scale(0.012) translate(-512, -512)">
                  <path
                    fill="currentColor"
                    d="M630.784 831.488c12.288 0 20.48-8.192 20.48-16.384l28.672-450.56c0-12.288-8.192-20.48-16.384-20.48-12.288 0-20.48 8.192-20.48 16.384l-28.672 450.56C614.4 823.296 622.592 831.488 630.784 831.488z"
                  ></path>
                  <path
                    fill="currentColor"
                    d="M409.6 831.488c12.288 0 20.48-8.192 16.384-20.48l-28.672-450.56c0-12.288-8.192-20.48-20.48-16.384C368.64 344.064 360.448 352.256 360.448 360.448l28.672 450.56C389.12 823.296 397.312 831.488 409.6 831.488z"
                  ></path>
                  <path
                    fill="currentColor"
                    d="M520.192 831.488c12.288 0 20.48-8.192 20.48-20.48l0-450.56c0-12.288-8.192-20.48-20.48-20.48-12.288 0-20.48 8.192-20.48 20.48l0 450.56C499.712 823.296 507.904 831.488 520.192 831.488z"
                  ></path>
                  <path
                    fill="currentColor"
                    d="M839.68 229.376l-188.416 0L651.264 151.552c0-20.48-16.384-36.864-36.864-36.864l-188.416 0c-20.48 0-36.864 16.384-36.864 36.864l0 73.728L200.704 225.28C188.416 229.376 180.224 237.568 180.224 245.76c0 12.288 8.192 20.48 20.48 20.48l36.864 0 36.864 602.112c4.096 40.96 32.768 73.728 73.728 73.728l339.968 0c40.96 0 69.632-32.768 73.728-73.728l36.864-602.112 36.864 0C851.968 266.24 860.16 258.048 860.16 245.76 860.16 237.568 851.968 229.376 839.68 229.376zM425.984 151.552 614.4 151.552l0 73.728-188.416 0L425.984 151.552zM729.088 868.352c-4.096 20.48-16.384 36.864-36.864 36.864L352.256 905.216c-20.48 0-32.768-16.384-36.864-36.864L274.432 266.24l491.52 0L729.088 868.352z"
                  ></path>
                </g>
              </svg>
              <span class="sm:mt-1 text-xs hidden sm:block">{{ isUpdating ? '更新中' : '实时更新' }}</span>
            </button>

            <!-- 隐私模式按钮 -->
            <button
              @click="togglePrivacyMode"
              class="flex sm:flex-col items-center text-gray-700 hover:text-[#fb7299] transition-colors duration-200"
            >
              <svg
                class="w-5 h-5 sm:w-6 sm:h-6"
                fill="none"
                viewBox="0 0 256 256"
                :stroke="isPrivacyMode ? '#fb7299' : 'currentColor'"
              >
                <path
                  d="M128,56C48,56,8,128,8,128s40,72,120,72s120-72,120-72S208,56,128,56Z"
                  :class="isPrivacyMode ? 'hidden' : 'block'"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="16"
                />
                <circle
                  cx="128"
                  cy="128"
                  r="32"
                  :class="isPrivacyMode ? 'hidden' : 'block'"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="16"
                />
                <path
                  d="M48,40L208,216"
                  :class="isPrivacyMode ? 'block' : 'hidden'"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="16"
                />
                <path
                  d="M154.9,157.6A32,32,0,0,1,97.6,100.3"
                  :class="isPrivacyMode ? 'block' : 'hidden'"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="16"
                />
                <path
                  d="M183.9,186.1C165.9,197.5,147.2,204,128,204,48,204,8,132,8,132s15.3-27.4,41.9-48.5"
                  :class="isPrivacyMode ? 'block' : 'hidden'"
                  fill="none"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="16"
                />
              </svg>
              <span class="sm:mt-1 text-xs hidden sm:block">{{ isPrivacyMode ? '关闭隐私' : '隐私模式' }}</span>
            </button>
          </div>

          <!-- 中间搜索框 -->
          <div class="flex-1 w-full mx-4 sm:mx-10">
            <SearchBar />
          </div>

          <!-- 右侧图标 -->
          <div class="flex items-center space-x-3 sm:space-x-6">
            <!-- 布局切换按钮 -->
            <button
              @click="$emit('change-layout', layout === 'list' ? 'grid' : 'list')"
              class="hidden sm:flex sm:flex-col items-center text-gray-700 hover:text-[#fb7299] transition-colors duration-200"
              :class="{ 'text-[#fb7299]': layout === 'grid' }"
            >
              <svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path
                  v-if="layout === 'list'"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 6h16M4 12h16M4 18h16"
                />
                <path
                  v-else
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6zM14 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zM14 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z"
                />
              </svg>
              <span class="sm:mt-1 text-xs hidden sm:block">{{ layout === 'list' ? '网格视图' : '列表视图' }}</span>
            </button>

            <!-- 批量操作按钮 -->
            <button
              @click="$emit('toggle-batch-mode')"
              class="flex sm:flex-col items-center text-gray-700 hover:text-[#fb7299] transition-colors duration-200"
              :class="{ 'text-[#fb7299]': isBatchMode }"
            >
              <svg class="w-5 h-5 sm:w-6 sm:h-6" fill="none" viewBox="0 0 24 24" :stroke="isBatchMode ? '#fb7299' : 'currentColor'">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 5a1 1 0 011-1h14a1 1 0 011 1v2a1 1 0 01-1 1H5a1 1 0 01-1-1V5zM4 13a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H5a1 1 0 01-1-1v-6zM16 13a1 1 0 011-1h2a1 1 0 011 1v6a1 1 0 01-1 1h-2a1 1 0 01-1-1v-6z" />
              </svg>
              <span class="sm:mt-1 text-xs hidden sm:block">{{ isBatchMode ? '点击取消' : '批量操作' }}</span>
            </button>

            <!-- 设置按钮 - 只在手机端显示 -->
            <button
              @click="$router.push('/settings')"
              class="flex sm:hidden flex-col items-center text-gray-700 hover:text-[#fb7299] transition-colors duration-200"
            >
              <svg class="w-5 h-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
              </svg>
            </button>
          </div>
        </div>

        <!-- 筛选区域 -->
        <div class="mx-auto transition-all duration-300 ease-in-out" :class="{'max-w-4xl': layout === 'list', 'max-w-6xl': layout === 'grid'}">
          <FilterDropdown
            :business="business"
            :businessLabel="businessLabel"
            :date="date"
            :category="category"
            :total="total"
            @update:business="$emit('update:business', $event)"
            @update:businessLabel="$emit('update:businessLabel', $event)"
            @update:date="$emit('update:date', $event)"
            @update:category="$emit('update:category', $event)"
            @click-date="$emit('click-date')"
            @click-category="$emit('click-category')"
            @refresh-data="$emit('refresh-data')"
          />
        </div>
      </div>
    </nav>
  </div>
</template>

<script setup>
import SearchBar from './SearchBar.vue'
import FilterDropdown from './FilterDropdown.vue'
import { ref, watch } from 'vue'
import { updateBiliHistoryRealtime } from '../../api/api'
import { showNotify } from 'vant'
import { usePrivacyStore } from '../../store/privacy'
import 'vant/es/notify/style'

const { isPrivacyMode, togglePrivacyMode } = usePrivacyStore()

const props = defineProps({
  date: {
    type: String,
    default: ''
  },
  total: {
    type: Number,
    default: 0
  },
  category: {
    type: String,
    default: ''
  },
  layout: {
    type: String,
    default: 'list'
  },
  isBatchMode: {
    type: Boolean,
    default: false
  },
  businessLabel: {
    type: String,
    default: ''
  },
  business: {
    type: String,
    default: ''
  }
})

const emit = defineEmits([
  'click-date',
  'click-category',
  'click-business',
  'change-layout',
  'update:date',
  'update:category',
  'update:business',
  'update:businessLabel',
  'refresh-data',
  'toggle-batch-mode'
])

const isUpdating = ref(false)
const syncDeleted = ref(localStorage.getItem('syncDeleted') === 'true')

// 监听 syncDeleted 的变化
watch(() => localStorage.getItem('syncDeleted'), (newVal) => {
  syncDeleted.value = newVal === 'true'
})

// 清除日期筛选
const clearDate = (event) => {
  event.stopPropagation()
  emit('update:date', '')
  emit('refresh-data')
}

// 清除分区筛选
const clearCategory = (event) => {
  event.stopPropagation()
  emit('update:category', '')
  emit('refresh-data')
}

// 清除条目类型筛选
const clearBusiness = (event) => {
  event.stopPropagation()
  emit('update:business', '')
  emit('update:businessLabel', '')
  emit('refresh-data')
}

// 处理更新
const handleUpdate = async () => {
  if (isUpdating.value) return

  isUpdating.value = true
  try {
    console.log('触发 refresh-data 事件')
    emit('refresh-data')
  } catch (error) {
    console.error('更新失败详细信息:', error)

    let errorMessage = '更新失败，请稍后重试'
    if (error.response?.data?.message) {
      errorMessage = error.response.data.message
    } else if (error.message) {
      errorMessage = error.message
    }

    showNotify({
      type: 'danger',
      message: errorMessage,
      duration: 3500,
    })
  } finally {
    console.log('更新流程结束')
    setTimeout(() => {
      isUpdating.value = false
    }, 2000) // 添加一个短暂延迟，提供更好的视觉反馈
  }
}
</script>

<style>
/* 全局滚动条样式 - 仅在PC端生效 */
@media (min-width: 1024px) {
  ::-webkit-scrollbar {
    width: 12px;
    height: 12px;
  }

  ::-webkit-scrollbar-track {
    background: #f6f7f8;
    border-radius: 6px;
  }

  ::-webkit-scrollbar-thumb {
    background: #e3e5e7;
    border-radius: 6px;
    border: 3px solid #f6f7f8;
    transition: all 0.2s ease-in-out;
  }

  ::-webkit-scrollbar-thumb:hover {
    background: #fb7299;
    border: 3px solid #f6f7f8;
  }

  ::-webkit-scrollbar-corner {
    background: #f6f7f8;
  }

  /* 移除深色模式滚动条样式，始终使用浅色模式 */
}

/* 如果需要添加特定样式可以在这里添加 */
</style>
