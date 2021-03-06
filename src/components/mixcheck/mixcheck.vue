<template>
  <div class="c-mixcheck">
    <slot></slot>
  </div>
</template>

<script type="text/ecmascript-6">
import { findComponentsDownward } from '@/common/lib/assist'
import { isArray } from '@/common/lib/utils'

export default {
  name: 'Mixcheck',
  props: {
    value: {
      type: [Array, String],
      default () {
        return ''
      }
    }
  },
  methods: {
    updateModel () {
      this.radioChildrens = findComponentsDownward(this, 'RadioItem')
      this.checkChildrens = findComponentsDownward(this, 'CheckItem')

      if (this.radioChildrens || this.checkChildrens) {
        const { value } = this
        const childrens = this.$children
        for (const child of childrens) {
          child.model = value
          child.currentValue = false
        }
        if (isArray(value)) {
          this.checkChildrens.forEach(child => {
            child.currentValue = value.indexOf(child.label) >= 0
          })
        } else {
          const index = this.radioChildrens.findIndex(ret => ret.label === value)
          if (index > -1) this.radioChildrens[index].currentValue = true
        }
      }
    },
    change (data) {
      this.currentValue = data
      this.$emit('input', data)
      this.$emit('on-change', data)
    }
  },
  watch: {
    value () {
      this.updateModel()
    }
  },
  data () {
    return {
      currentValue: this.value,
      radioChildrens: [],
      checkChildrens: []
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.updateModel()
    })
  }
}
</script>

<style lang="stylus" scoped>
@import "~@/common/styles/mixin.styl"

.c-mixcheck
  position relative
  display inline-block
  vertical-align middle
  font-size 0
  >>> .mixcheck-item
    display inline-block
    font-size 12px
    height 32px
    line-height 30px
    padding 0px 15px
    color #333
    cursor pointer
    user-select none
    background-color #fff
    border 1px solid #dcdee2
    border-left 0
    &:first-child
      border-left 1px solid #dcdee2
      border-top-left-radius 4px
      border-bottom-left-radius 4px
    &:last-child
      border-top-right-radius 4px
      border-bottom-right-radius 4px
    &.c-wrapper-checked
      color #2b85e4
      border-color #2b85e4
      box-shadow -1px 0 0 0 #2d8cf0
      z-index 1
      &:first-child
        box-shadow none
      &:after
        border-left-color #2b85e4
    &.c-wrapper-disabled
      color #ffffff
      background-color #e6e6e6
      border-color #dcdee2
      cursor not-allowed
      box-shadow none!important
      &:first-child
       border-left-color #dcdee2
    .c-mix-input
      opacity 0
      width 0
      height 0
</style>
