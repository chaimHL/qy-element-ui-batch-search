<!-- 批量搜索框 -->
<template>
  <div style="position: relative">
    <el-popover
      placement="bottom-start"
      :width="minWidth"
      trigger="click"
      @show="onPopoverShow"
    >
      <!-- 默认隐藏的批量输入框 -->
      <el-input
        ref="multilineDataRef"
        v-model="multilineData"
        type="textarea"
        :autosize="{ minRows: 4, maxRows: 8 }"
        maxlength="3000"
        show-word-limit
        :placeholder="$t('qingshuru')"
        clearable
        @input="onMultilineDataInput"
      />
      <template #reference>
        <div
          ref="referenceBoxRef"
          class="reference-box"
          :style="{ width: isMulti ? minWidth + 'px' : '' }"
        />
      </template>
    </el-popover>
    <i class="el-input__icon el-icon-tickets" :style="{ color: iconColor }" />
    <!-- 默认显示的单行输入框 -->
    <el-input
      v-model="singleLineData"
      class="single-line-input"
      :placeholder="$t('qingshuru')"
      @change="onChangeSingleLineInput"
      @input="onSingleDataInput"
      @keyup.enter.native="onEnter"
    />
    <i
      v-if="singleLineData"
      class="el-icon-circle-close close-icon"
      @click="handleClear"
    />
  </div>
</template>

<script>
export default {
  name: 'QyElementUiBatchSearch',
  data() {
    return {
      minWidth: '',
      singleLineData: '',
      screenWidth: '',
      iconColor: '#c0c4cc',
      multilineData: '',
      isMulti: false
    }
  },
  watch: {
    screenWidth() {
      this.setWidth()
    }
  },
  mounted() {
    this.$nextTick(() => {
      this.setWidth()
    })
    window.addEventListener('resize', () => {
      return (() => {
        window.screenWidth = document.body.clientWidth
        this.screenWidth = window.screenWidth
      })()
    })
  },
  methods: {
    onEnter() {
      this.singleLineData = this.singleLineData + ' '
      this.singleLineData + this.$refs.referenceBoxRef.click()
    },
    setWidth() {
      this.minWidth = this.$children[1].$el?.getBoundingClientRect().width
    },
    setSingleLine() {
      this.iconColor = '#c0c4cc'
      this.isMulti = false
    },
    onSingleDataInput(val) {
      // 如果是批量查询，直接展开 Popover
      if (val.search(/[\s\n]/) !== -1) {
        this.$refs.referenceBoxRef.click()
      }
      if (!val) {
        this.multilineData = ''
        this.$emit('clear-input')
      }
    },
    onChangeSingleLineInput() {
      if (this.singleLineData && !this.isMulti) {
        if (this.singleLineData.search(/[\s\n]/) === -1) {
          // 输入的是单条数据
          this.$emit('change-input', this.singleLineData)
        }
      }
    },
    // 多行输入时
    onMultilineDataInput(val) {
      if (val) {
        if (val.search(/[\s\n]/) === -1) {
          // 输入的是单行数据
          this.singleLineData = val
          this.setSingleLine()
        } else {
          // 输入了多行数据
          this.modifyFormatAndStyle(val)
        }
        this.$emit('change-input', val)
      } else {
        this.singleLineData = ''
        this.setSingleLine()
        this.$emit('clear-input')
      }
    },
    onPopoverShow() {
      this.$nextTick(() => {
        this.$refs.multilineDataRef.$el
          .getElementsByTagName('textarea')[0]
          .focus()
      })
      // 如果是直接在单行输入框输入的批量查询，那么让 Popover 的内容等于单行输入框的内容
      if (!this.multilineData) {
        if (this.singleLineData) {
          // 将所有空格和逗号换成回车
          const content = this.singleLineData.replace(/[\s]/g, '\n')
          this.multilineData = content
          this.$emit('change-input', this.singleLineData)
          this.modifyFormatAndStyle(this.singleLineData)
        } else {
          this.multilineData = ''
        }
      }
    },
    // 将单行输入框的格式修改为 xxx...，并修改 icon 颜色
    modifyFormatAndStyle(val) {
      this.iconColor = '#46a6ff'
      this.isMulti = true
      const tempArr = val.split(/[\s\n]/).filter(item => item)
      // 这一步顺序一定要在 this.isMulti = true 之后
      if (tempArr[0]) {
        this.singleLineData =
          tempArr[0]?.slice(0, 30).trim() +
          '...' +
          this.$i18n.t('deng') +
          tempArr.length +
          this.$i18n.t('xiang')
      }
    },
    handleClear() {
      this.singleLineData = this.multilineData = ''
      this.setSingleLine()
      this.$emit('clear-input')
    }
  }
}
</script>

<style scoped lang="scss">
.reference-box {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 7;
  width: 30px;
  cursor: pointer;
}

.el-input__icon {
  position: absolute;
  top: -4px;
  left: 6px;
  z-index: 6;
}

.close-icon {
  position: absolute;
  right: 8px;
  z-index: 8;
  color: rgb(192, 196, 204);
  &:hover {
    cursor: pointer;
    color: #aba6a5;
  }
}

.single-line-input::v-deep {
  input {
    padding-left: 30px;
  }
  .el-input__suffix {
    display: block !important;
  }
}

.el-form-item--default {
  .el-input__icon {
    top: 0;
  }
  .close-icon {
    top: 12px;
  }
  .reference-box {
    height: 40px;
  }
}

.el-form-item--medium {
  .el-input__icon {
    top: -2px;
  }
  .close-icon {
    top: 12px;
  }
  .reference-box {
    height: 36px;
  }
}

.el-form-item--small {
  .close-icon {
    top: 9px;
  }
  .reference-box {
    height: 32px;
  }
}

.el-form-item--mini {
  .el-input__icon {
    top: -5px;
  }
  .close-icon {
    top: 8px;
  }
  .reference-box {
    height: 28px;
  }
}
</style>
