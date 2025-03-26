<script lang="ts" setup>
import { browser } from 'wxt/browser'
import { type ComputedRef, ref, watch } from 'vue'
import { useDateFormat, useElementHover, useNow } from '@vueuse/core'

import { useSettingsStore } from '@/newtab/scripts/store'

const settingsStore = useSettingsStore()
const time = ref()
const isTimeHovered = useElementHover(time)

watch(isTimeHovered, (isTimeHovered) => {
  time.value.style.transform = isTimeHovered ? 'scale(1.1)' : null
})

const timeNow = useNow({ interval: 1000 })
const lang = browser.i18n.getUILanguage()
const isChinese = lang.startsWith('zh')

const timeNowHour: ComputedRef<string> = useDateFormat(timeNow, 'HH')
const timeNowMinute = useDateFormat(timeNow, 'mm')
const timeNowWeekday = useDateFormat(timeNow, 'dddd')
</script>

<template>
  <div
    ref="time"
    class="clock"
    :class="[
      settingsStore.time.enableShadow ? 'shadow' : undefined,
      settingsStore.time.invertColor.light ? ['invert', 'light'] : undefined,
      settingsStore.time.invertColor.night ? ['invert', 'night'] : undefined
    ]"
  >
    <div class="time">
      <span>
        <span class="hour">{{ timeNowHour }}</span>
        <span class="colon">:</span>
        <span class="minute">{{ timeNowMinute }}</span>
      </span>
    </div>
    <div class="date">
      <span>
        {{
          timeNow.toLocaleDateString(undefined, {
            dateStyle: 'long'
          })
        }}
        {{ timeNowWeekday }}
      </span>
    </div>
  </div>
</template>

<style scoped lang="scss">
@keyframes twinkle {
  0%,
  100% {
    opacity: 0.5;
  }
  50% {
    opacity: 1;
  }
}

@keyframes delayedFadeIn {
  0%,
  50% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

.clock {
  text-align: center;
  color: var(--el-fill-color-blank);
  animation: delayedFadeIn 0.5s;
  transition:
    font-size 0.25s cubic-bezier(0.5, 0, 0.5, 2),
    transform 0.25s cubic-bezier(0.5, 0, 0.5, 2),
    text-shadow var(--el-transition-duration-fast) ease,
    color var(--el-transition-duration-fast) ease;

  &.shadow {
    text-shadow: 0 6px 16px rgba(0, 0, 0, 0.4);
  }

  .time {
    font-size: 60px;
  }

  .date {
    margin-bottom: 5px;
  }

  &.invert.light,
  html.dark & {
    color: var(--el-text-color-primary);
  }

  html.dark &.invert.night {
    color: var(--el-fill-color-extra-light);
  }

  .colon {
    animation: twinkle 1s ease infinite;
  }
}

@media screen and (max-width: 600px) {
  .clock {
    .time {
      font-size: 50px;
    }

    .date {
      font-size: 13px;
    }
  }
}
</style>