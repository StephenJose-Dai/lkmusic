<template lang="pug">
dt#about {{$t('setting__about')}}
dd
  p.small
    | 你已签署本软件的&nbsp;
    base-btn(min @click="handleShowPact") 许可协议
    | ，协议的在线版本在&nbsp;
    strong.hover.underline(:aria-label="$t('setting__click_open')" @click="openUrl('https://pornhub.com/')") 这里
    | &nbsp;。
  br

  p
    small By：
    | StephenJose_Dai
</template>

<script>
// import { ref, onBeforeUnmount } from '@renderer/utils/vueTools'
import { isShowPact } from '@renderer/core/share'
import { openUrl, clipboardWriteText } from '@renderer/utils'
import { currentStting } from '../setting'

export default {
  name: 'SettingAbout',
  setup() {
    const handleShowPact = () => {
      isShowPact.value = true
    }
    return {
      currentStting,
      openUrl,
      clipboardWriteText,
      handleShowPact,
    }
  },
}
</script>
