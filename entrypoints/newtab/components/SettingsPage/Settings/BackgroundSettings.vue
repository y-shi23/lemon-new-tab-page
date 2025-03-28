<script setup lang="ts">
import { PictureOutlined } from '@vicons/antd'
import { Plus } from '@vicons/fa'
import { type UploadProps, type UploadRequestOptions } from 'element-plus'

import { i18n } from '@/.wxt/i18n'
import { isImageFile } from '@/newtab/scripts/img'
import { uploadBackgroundImage, useSettingsStore } from '@/newtab/scripts/store'
import { BgType } from '@/newtab/scripts/settings'

const settingsStore = useSettingsStore()

const beforeBackgroundUpload: UploadProps['beforeUpload'] = (rawFile) => {
  if (!isImageFile(rawFile)) {
    ElMessage.error(i18n.t('newtab.settings.background.warning.fileIsNotImage'))
    return false
  }
  return true
}
</script>

<template>
  <div class="settings-title">
    <el-icon><picture-outlined /></el-icon>
    <span>{{ i18n.t('newtab.settings.background.title') }}</span>
  </div>
  <div class="setting-items-container">
    <div class="settings-item">
      <div class="settings-label">{{ i18n.t('newtab.settings.background.type.title') }}</div>
      <el-radio-group v-model="settingsStore.background.bgType">
        <el-radio :value="BgType.None">{{ i18n.t('newtab.settings.background.type.none') }}</el-radio>
        <el-radio :value="BgType.Local">{{ i18n.t('newtab.settings.background.type.local') }}</el-radio>
      </el-radio-group>
    </div>
    <el-upload
      v-if="settingsStore.background.bgType === BgType.Local"
      class="bg-uploader"
      :show-file-list="false"
      :http-request="(option: UploadRequestOptions) => uploadBackgroundImage(option.file)"
      :before-upload="beforeBackgroundUpload"
      accept="image/*"
    >
      <img
        v-if="settingsStore.localBackground.url"
        :src="settingsStore.localBackground.url"
        class="bg-uploader__img"
      />
      <el-icon v-else class="bg-uploader__icon"><plus /></el-icon>
    </el-upload>
    <div class="settings-item horizontal">
      <div class="settings-label">{{ i18n.t('newtab.settings.background.enableVignetting') }}</div>
      <el-switch v-model="settingsStore.background.enableVignetting" />
    </div>
    <div v-if="settingsStore.background.bgType !== BgType.None" class="settings-item">
      <div class="settings-label">{{ i18n.t('newtab.settings.background.blur') }}</div>
      <el-slider v-model="settingsStore.background.blurIntensity" :show-tooltip="false" />
    </div>
    <div class="settings-item">
      <div class="settings-label">{{ i18n.t('newtab.settings.background.maskOpacity') }}</div>
      <el-slider v-model="settingsStore.background.bgMaskPpacity" :show-tooltip="false" />
    </div>
    <div class="settings-item horizontal">
      <div class="settings-label">{{ i18n.t('newtab.settings.background.maskColor') }}</div>
      <el-color-picker
        v-model="settingsStore.background.maskColor"
        :predefine="['#fff', '#000']"
        @change="
          () => {
            if (settingsStore.background.maskColor === null) {
              settingsStore.background.maskColor = '#000'
            }
          }
        "
      />
    </div>
  </div>
</template>

<style scoped lang="scss">
.bg-uploader__img {
  max-width: 100%;
  object-fit: cover;
}

.bg-uploader:deep() .el-upload {
  border: 1px dashed var(--el-border-color-darker);
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: var(--el-transition-duration-fast);

  &:hover {
    border-color: var(--el-color-primary);

    .el-icon.bg-uploader__icon {
      color: var(--el-color-primary);
    }
  }
}

.el-icon.bg-uploader__icon {
  font-size: 28px;
  color: var(--el-text-color-placeholder);
  width: 350px;
  height: 200px;
  text-align: center;
  transition: var(--el-transition-duration-fast);
}

:deep().el-color-picker__trigger {
  padding: 0;
  overflow: hidden;
  border-radius: 8px;

  .el-color-picker__color {
    border: none;
    border-radius: none;
  }
}
</style>
