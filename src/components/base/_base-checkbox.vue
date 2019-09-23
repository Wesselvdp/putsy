<template>
  <label class="checkbox__label">
    <input
      id="checkboxTerms"
      :class="['checkbox', {'checked': $attrs.value}]"
      v-bind="$attrs"
      name="checkbox"
      :data-omni-type="$microsite"
      :data-omni="`nl:${$project}:contact-details:terms-conditions-check`"
      type="checkbox"
      @change="handleChange($event.target.checked)">

    <slot />
  </label>
</template>
<script>
export default {
  name: 'BaseCheckbox',
  inheritAttrs: false,
  model: {
    event: 'update',
  },
  data() {
    return {
      checked: false,
    };
  },
  methods: {
    handleChange(val) {
      this.$emit('update', val);
    },
  },
};
</script>

<style lang="scss" scoped>
.checkbox {
  -webkit-appearance: none;
  width: 2em;
  height: 2em;
  margin-right: 5px;
  margin-bottom: 0;
  background-color: white;
  border-radius: 50%;
  margin-right: 0.5em;
  position: relative;
  transform: translateY(25%);

  &:focus {
    outline: none;
  }

  &::after {
    content: "";
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    border-radius: 3px;
    box-shadow: 0 0 2px 0 rgba(8, 0, 0, 0.9);
    background-color: #fff;
    transition: all 0.15s ease;
  }

  &.checked::after {
    background-color: #000;
    border: 4px solid white;
    border-radius: 3px;
    left: 0px;
    right: 0px;
    top: 0px;
    bottom: 0px;
  }

  &__label {
    margin-bottom: 1.5em;
    display: inline-block;
  }
}
</style>
