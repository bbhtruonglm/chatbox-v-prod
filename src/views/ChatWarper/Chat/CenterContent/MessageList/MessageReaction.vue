<template>
  <div
    id="chat__message-date"
    class="text-xxs text-slate-500 absolute group w-max z-10 -bottom-3.5"
    :class="sender_id === fb_page_id ? 'right-0' : 'left-0'"
  >
    <!-- Trigger icon: chỉ hiện khi hover message -->
    <span
      class="flex items-center justify-center px-3 py-0.5 bg-white rounded-lg cursor-pointer text-base opacity-0 group-hover:opacity-100 transition-opacity duration-150 relative"
      @mouseenter="openReactions"
      @mouseleave="closeReactions"
      ref="triggerIcon"
    >
      👍

      <!-- Reaction popup: hiện khi hover trigger icon -->
      <div
        v-if="is_open"
        ref="reactionPopup"
        class="absolute z-50 bg-white border border-slate-200 rounded-full shadow-lg p-1 flex gap-1 transition-all duration-150"
        :style="popupStyle"
        @mouseenter="hovering = true"
        @mouseleave="closeReactions"
      >
        <span
          v-for="reaction in mainReactions"
          :key="reaction.code"
          class="cursor-pointer hover:scale-125 transition-transform w-10 h-10 rounded-full flex items-center justify-center text-2xl"
          @click="selectReaction(reaction.code)"
        >
          {{ reaction.icon }}
        </span>
      </div>
    </span>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
/** Khai báo các biến trạng thái cho popup reaction */
const is_open = ref(false)
const hovering = ref(false)
const popupStyle = ref({} as any)
const triggerIcon = ref<HTMLElement | null>(null)
/** Khai báo kiểu dữ liệu cho reaction */
interface Reaction {
  /** code của reaction */
  code: string
  /** icon của reaction */
  icon: string
  /** màu sắc của reaction */
  color: string
}

/** 6 main reactions (UTF-8 emoji) */
const mainReactions: Reaction[] = [
  { code: '/-strong', icon: '👍', color: '#ffffff' },
  { code: '/-heart', icon: '❤️', color: '#ffffff' },
  { code: ':>', icon: '😆', color: '#ffffff' },
  { code: ':-h', icon: '😡', color: '#ffffff' },
  { code: '/-bome', icon: '🎉', color: '#ffffff' },
  { code: ':o', icon: '😮', color: '#ffffff' }, // Surprise
]
/** Hàm mở popup reaction */
function openReactions() {
  is_open.value = true
  updatePopupPosition()
}
/** Hàm đóng popup reaction */
function closeReactions() {
  setTimeout(() => {
    if (!hovering.value) {
      is_open.value = false
    }
  }, 100)
  hovering.value = false
}
/** Hàm cập nhật vị trí của popup reaction */
function updatePopupPosition() {
  /** Nếu có trigger icon */
  if (triggerIcon.value) {
    /** Lấy vị trí của trigger icon */
    const RECT = triggerIcon.value.getBoundingClientRect()
    /** Tính toán vị trí của popup */
    const TOP = RECT.top - 55
    /**  Tính toán vị trí bên trái của popup */
    const LEFT = Math.min(window.innerWidth - 220, Math.max(10, RECT.left - 20))
    /** Cập nhật vị trí của popup */
    popupStyle.value = {
      top: `${TOP}px`,
      left: `${LEFT}px`,
      position: 'fixed',
    }
  }
}
/** Hàm chọn reaction */
function selectReaction(code: string) {
  console.log('Selected reaction code:', code)
  is_open.value = false
}
/** Props */
const $props = withDefaults(
  defineProps<{
    sender_id?: string
    fb_page_id?: string
  }>(),
  {}
)
</script>

<style scoped>
/* transition mềm cho icon trigger */
</style>
