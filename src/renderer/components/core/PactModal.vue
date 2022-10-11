<template lang="pug">
material-modal(:show="!setting.isAgreePact || isShowPact" @close="handleClose(false)" :bgClose="setting.isAgreePact" :close-btn="setting.isAgreePact")
  main(:class="$style.main")
    h2 许可协议
    div.select.scroll(:class="$style.content")

      p
        strong 禁止在违反当地法律法规的情况下使用本软件
        |，对于使用者在明知或不知当地法律法规不允许的情况下使用本软件所造成的任何违法违规行为由使用者承担，本软件不承担由此造成的任何直接、间接、特殊、偶然或结果性责任。
      br
      p
        strong *
        | &nbsp;若协议更新，恕不另行通知。
      p(v-if="!setting.isAgreePact")
        strong 若你（使用者）接受以上协议，请点击下面的“接受”按钮签署本协议，若不接受，请点击“不接受”后退出软件并清除本软件的所有数据。


    div(:class="$style.btns" v-if="!setting.isAgreePact")
      base-btn(:class="$style.btn" @click="handleClose(true)") {{$t('not_agree')}}
      base-btn(:class="$style.btn" :disabled="!btnEnable" @click="handleClick()") {{$t('agree')}} {{timeStr}}
</template>

<script>
import { mapGetters, mapMutations } from 'vuex'
import { rendererSend, NAMES } from '@common/ipc'
import { openUrl } from '@renderer/utils'
import { isShowPact } from '@renderer/core/share'

export default {
  setup() {
    return {
      isShowPact,
    }
  },
  data() {
    return {
      time: 20,
    }
  },
  computed: {
    ...mapGetters(['setting']),
    btnEnable() {
      return this.time == 0
    },
    timeStr() {
      return this.btnEnable ? '' : `(${this.time})`
    },
  },
  watch: {
    'setting.isAgreePact'(n) {
      if (n) return
      this.time = 5
      this.startTimeout()
    },
  },
  mounted() {
    this.$nextTick(() => {
      if (!this.setting.isAgreePact) {
        this.startTimeout()
      }
    })
  },
  methods: {
    ...mapMutations(['setAgreePact']),
    handleClick() {
      this.setAgreePact()
      setTimeout(() => {
        this.$dialog({
          message: Buffer.from('e69cace8bdafe4bbb6e5ae8ce585a8e5858de8b4b9e4b894e5bc80e6ba90efbc8ce5a682e69e9ce4bda0e698afe88ab1e992b1e8b4ade4b9b0e79a84efbc8ce8afb7e79bb4e68ea5e7bb99e5b7aee8af84efbc810a0a5468697320736f667477617265206973206672656520616e64206f70656e20736f757263652e', 'hex').toString(),
          confirmButtonText: Buffer.from('e5a5bde79a8420284f4b29', 'hex').toString(),
        })
      }, 2e3)
    },
    handleClose(isExit) {
      if (isExit) return rendererSend(NAMES.mainWindow.close, true)
      isShowPact.value = false
    },
    openUrl(url) {
      openUrl(url)
    },
    startTimeout() {
      setTimeout(() => {
        if (--this.time > 0) this.startTimeout()
      }, 1e3)
    },
  },
}
</script>


<style lang="less" module>
@import '@renderer/assets/styles/layout.less';

.main {
  padding: 15px;
  max-width: 550px;
  min-width: 200px;
  min-height: 0;
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  h2 {
    font-size: 16px;
    color: @color-theme_2-font;
    line-height: 1.3;
    text-align: center;
  }
}

.content {
  flex: auto;
  margin: 15px 0;
  padding-right: 5px;
  h3 {
    font-weight: bold;
    line-height: 2;
  }
  p {
    line-height: 1.5;
    font-size: 14px;
    text-align: justify;
  }
}

.btns {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.btn {
  display: block;
  width: 48%;
  &:last-child {
    margin-bottom: 0;
  }
}

each(@themes, {
  :global(#root.@{value}) {
    .main {
      h2 {
        color: ~'@{color-@{value}-theme_2-font}';
      }
    }
    .name {
      color: ~'@{color-@{value}-theme}';
    }
  }
})

</style>
