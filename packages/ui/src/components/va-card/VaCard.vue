<template>
  <component
    :is="tagComputed"
    class="va-card"
    :class="classComputed"
    :style="cardStyles"
    :href="hrefComputed"
    :target="target"
    :to="to"
    :replace="replace"
    :exact="exact"
    :active-class="activeClass"
    :exact-active-class="exactActiveClass"
  >
    <div v-if="stripe" class="va-card__stripe" :style="stripeStyles" />
    <div class="va-card__inner" @click="$emit('click', $event)">
      <slot />
    </div>
  </component>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue'

import pick from 'lodash/pick.js'
import { getGradientBackground } from '../../services/color'
import {
  useBem,
  useComponentPresetProp,
  useColors,
  useTextColor,
  useRouterLink,
  useRouterLinkProps,
} from '../../composables'

export default defineComponent({
  name: 'VaCard',
  emits: ['click'],
  props: {
    ...useRouterLinkProps,
    ...useComponentPresetProp,
    tag: { type: String, default: 'div' },
    square: { type: Boolean, default: false },
    outlined: { type: Boolean, default: false },
    bordered: { type: Boolean, default: true },
    disabled: { type: Boolean, default: false },
    href: { type: String, default: '' },
    target: { type: String, default: '' },
    stripe: { type: Boolean, default: false },
    stripeColor: { type: String, default: '' },
    gradient: { type: Boolean, default: false },
    textColor: { type: String },
    color: { type: String, default: 'background-secondary' },
  },
  setup (props) {
    const { getColor } = useColors()
    const { isLinkTag, tagComputed, hrefComputed } = useRouterLink(props)
    const { textColorComputed } = useTextColor()

    const stripeStyles = computed(() => ({ background: getColor(props.stripeColor) }))

    const classComputed = useBem('va-card', () => ({
      ...pick(props, ['square', 'outlined', 'disabled']),
      noBorder: !props.bordered,
      link: isLinkTag.value,
    }))

    const cardStyles = computed(() => {
      const background = props.gradient && props.color
        ? getGradientBackground(getColor(props.color))
        : getColor(props.color)

      return {
        background,
        color: textColorComputed.value,
      }
    })

    return {
      classComputed,
      cardStyles,
      stripeStyles,
      tagComputed,
      hrefComputed,
    }
  },
})
</script>

<style lang="scss">
@import "../../styles/resources";
@import "variables";

.va-card {
  display: var(--va-card-display);
  position: var(--va-card-position);
  overflow: var(--va-card-overflow);
  box-shadow: var(--va-card-box-shadow, var(--va-block-box-shadow));
  border-radius: var(--va-card-border-radius, var(--va-block-border-radius));
  color: var(--va-card-color);
  background-color: var(--va-card-background-color);
  font-family: var(--va-font-family);

  &__inner {
    width: 100%;
    height: 100%;

    & > div:first-child {
      border-top-right-radius: var(--va-card-border-radius);
      border-top-left-radius: var(--va-card-border-radius);
    }

    & > div:last-child {
      border-bottom-right-radius: var(--va-card-border-radius);
      border-bottom-left-radius: var(--va-card-border-radius);
    }
  }

  &--square {
    border-radius: 0;
  }

  &--outlined {
    box-shadow: var(--va-card-outlined-box-shadow);
    border: var(--va-card-outlined-border, var(--va-block-border));
  }

  &--no-border {
    border: none;
  }

  &--disabled {
    @include va-disabled;
  }

  &--link {
    cursor: pointer;
  }

  &__stripe {
    content: "";
    position: absolute;
    width: 100%;
    height: var(--va-card-stripe-border-size);
    top: 0;
    left: 0;
  }

  &__title,
  &__content,
  &__actions,
  &__actions--vertical {
    padding: var(--va-card-padding);

    + .va-card__title,
    + .va-card__content,
    + .va-card__actions,
    + .va-card_actions__vertical {
      padding-top: 0;
    }
  }

  &__title {
    display: flex;
    align-items: center;

    @include va-title();
  }
}
</style>
