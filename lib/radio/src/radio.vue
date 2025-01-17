<template>
  <label class="le-radio"
    :class="[
      border && radioSize ? 'le-radio-' + radioSize : '',
      {
        'is-focus': focus,
        'is-checked': isEqual,
        'is-bordered': border,
        'is-disabled': isDisabled
      }
    ]"
    :tabindex="tabIndex"
  >
    <span class="le-radio-input"
      :class="{
        'is-checked': isEqual,
        'is-disabled': isDisabled
      }"
    >
      <span class="le-radio-inner"></span>
      <input
        ref='radio'
        class="le-radio-original"
        type="radio"
        aria-hidden='true'
        :value="label"
        v-model="model"
        @focus='focus = true'
        @blur='focus = false'
        @change="handleChange"
        :disabled="isDisabled"
        tabindex='-1'
      >
    </span>
    <span class='le-radio-label'>
      <slot />
      <template v-if="!$slots.default">{{ label }}</template>
    </span>
  </label>
</template>

<script setup>
import { computed, defineEmit, defineProps, ref, getCurrentInstance, watchEffect } from "vue"

const instance = getCurrentInstance()
const radioGroup = ref()

const prop = defineProps({
  label: {},
  modelValue: {},
  disabled: Boolean,
  name: String,
  border: Boolean,
  size: String
})

const emit = defineEmit([
  'update:modelValue'
])

const radio = ref()

const model = computed({
  get () {
    return isGroup.value ? radioGroup.value.modelValue : prop.modelValue
  },
  set (val) {
    if (isGroup.value) {
      radioGroup.value.$emit('update:modelValue', val)
    } else {
      emit('update:modelValue', val)
    }
    radio.value && (radio.value.checked = isEqual.value)
  }
})

const isGroup = computed(() => {
  let parent = instance.parent
  while (parent) {
    if (parent.ctx.componentName !== 'LeRadioGroup') {
      parent = parent.parent
    } else {
      radioGroup.value = parent.ctx
      return true
    }
  }
  return false
})

const isEqual = computed(() => {
  return (model.value|0) === (prop.label|0)
})

const isDisabled = computed(() => {
  return isGroup.value
    ? radioGroup.value.$props.disabled || prop.disabled
    : prop.disabled
})

const tabIndex = computed(() => {
  return isDisabled.value ? -1 : 0
})

const radioSize = computed(() => {
  return prop.size
})

const focus = ref(false)

function handleChange () {
  emit('change', model.value)
}

</script>

<style lang="stylus" scoped>
@import '../../../src/styles/global.styl'
@import '../../../src/styles/var.styl'
.le-radio
  line-height 1
  cursor pointer
  display inline-block
  white-space nowrap
  font-size $font-size-base
  margin-right 30px
  &:last-child
    margin-right 0
  .le-radio-input
    white-space nowrap
    cursor pointer
    outline none
    display inline-block
    line-height 1
    position relative
    vertical-align middle
    /&.is-checked
      .le-radio-inner
        border-color $radio-checked-input-border-color
        background $radio-checked-icon-color
        &::after
          transform translate(-50%, -50%) scale(1)
      & + .le-radio-label
        color $radio-checked-font-color
    /&.is-disabled
      .le-radio-inner
        background-color $radio-disabled-input-fill
        border-color $radio-disabled-input-border-color
        cursor not-allowed
        &::after
          cursor not-allowed
          background-color $radio-disabled-icon-color
        & + .le-radio-label
          cursor not-allowed
      &.is-checked
        .le-radio-inner
          background-color $radio-disabled-checked-input-fill
          border-color $radio-disabled-checked-input-border-color
          &::after
            background-color $radio-disabled-checked-icon-color
      & + span.le-radio-label
        color $color-text-placeholder
        cursor not-allowed
    .le-radio-inner
      border $radio-input-border
      border-radius $radio-input-border-radius
      width $radio-input-width
      height $radio-input-height
      background-color $radio-input-background-color
      position relative
      cursor pointer
      display inline-block
      box-sizing border-box
      &::after
        square(4px)
        border-radius @border-radius
        background-color $color-white
        content ""
        position absolute
        left 50%
        top 50%
        transform translate(-50%, -50%) scale(0)
        transition transform .15s ease-in
    .le-radio-original
      opacity 0
      outline none
      position absolute
      z-index -1
  .le-radio-label
    font-size $radio-font-size
    padding-left 10px
  /&.is-bordered
    padding $radio-bordered-padding
    border-radius $border-radius-base
    border $border-base
    box-sizing border-box
    height $radio-bordered-height
    &.is-checked
      border-color $color-primary
    &.is-disabled
      cursor not-allowed
      border-color $border-color-lighter
    & + .le-radio.is-bordered
      margin-left 10px
.le-radio-medium
  &.is-bordered
    padding $radio-bordered-medium-padding
    border-radius $button-medium-border-radius
    height $radio-bordered-medium-height
    .le-radio-label
      font-size $button-medium-font-size
    .le-radio-inner
      height $radio-bordered-medium-input-height
      width $radio-bordered-medium-input-width
.le-radio-small
  &.is-bordered
    padding $radio-bordered-small-padding
    border-radius $button-small-border-radius
    height $radio-bordered-small-height
    .le-radio-label
      font-size $button-small-font-size
    .le-radio-inner
      height $radio-bordered-small-input-height
      width $radio-bordered-small-input-width
.le-radio-mini
  &.is-bordered
    padding $radio-bordered-mini-padding
    border-radius $button-mini-border-radius
    height $radio-bordered-mini-height
    .le-radio-label
      font-size $button-mini-font-size
    .le-radio-inner
      height $radio-bordered-mini-input-height
      width $radio-bordered-mini-input-width
</style>