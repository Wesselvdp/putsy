<template>
  <div :class="['putsy putsy--input putsy__outer', {'disabled': disabled}, {'focus': focus}, {'invalid': true}]">
    <!-- Label -->
    <label
      :for="name"
      :class="['putsy__label', {'dirty': dirty}]">
      {{ label }}
    </label>

    <!-- inner -->
    <div :class="['putsy__fieldwrap', {'focus': focus}]">
       <!-- Appendix / Optional -->
      <span
        v-if="suffix"
        class="putsy__appendix"
        @click="$emit('suffixCLick')">
        <!-- <i class="material-icons">
          {{ appendix }}
        </i> -->
        {{ suffix}}
      </span>

      <input
        :id="name"
        v-bind="$attrs"
        :name="name"
        :label="label"
        :class="['putsy__field', {'invalid' : error}]"
        :maxlength="maxLength"
        :focus="focus"
        @focus="focus = true"
        @input="$emit('update', $event.target.value)"
        @blur="blur($event.target.type)">

      <!-- Appendix / Optional -->
      <span
        v-if="appendix"
        class="putsy__appendix"
        @click="$emit('appendixCLick')">
        <!-- <i class="material-icons">
          {{ appendix }}
        </i> -->

        {{ appendix }}
      </span>
    </div>
    <!-- / Inner -->

    <!-- Counter -->
    <div
      v-if="counter"
      :class="['putsy__counter', {'maxed': tooLong}]">
      {{ maxLength - $attrs.value.length }}/{{ maxLength }} Tekens
    </div>
  </div>
</template>
<script>
export default {
  name: 'BaseInput',
  inheritAttrs: false,
  model: {
    event: 'update',
  },
  props: {
    maxLength: {
      type: Number,
      default: null,
    },
    counter: {
      type: Boolean,
      default: false,
    },
    suffix: {
      type: String,
      default: null,
    },
    required: {
      type: Boolean,
      default: false,
    },
    invalid: {
      type: Boolean,
      default: false,
    },
    appendix: {
      type: String,
      default: null,
    },
    label: {
      type: String,
      default: null,
    },
    name: {
      type: String,
      default: null,
    },
    placeholder: {
      type: String,
      default: null,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    light: {
      type: Boolean,
      default: false,
    },
    hasError: {
      type: Boolean,
      default: false,
    },
    imei: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      value: '',
      focus: false,
      filled: false,
      error: false,
    };
  },
  computed: {
    dirty() {
      return !(this.focus || this.$attrs.value || this.value);
    },
    
    theLength() {
      return this.$attrs.value ? this.$attrs.value.length : null;
    },

    tooLong() {
      const check = this.value.length > this.maxLength;
      if (check) this.giveError();
      return check;
    },

    pattern() {
      return this.type === 'number' ? 'd*' : '';
    },

    emailValidation() {
      const email = this.$attrs.value;
      const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      if (email.length === 0 || re.test(String(email).toLowerCase())) {
        return true;
      }
      return false;
    },
  },

  methods: {
    blur(type) {
      if (type === 'email' && !this.emailValidaton) this.giveError();
      this.focus = false;
    },

    // Error animation
    giveError() {
      this.error = true;
      setTimeout(() => { (this.error = false); }, 1000);
    },
  },
};
</script>

<style lang="scss">
//Colors
$color-samsung-blue: blue;
$color-grey-1: grey;
$color-grey-2: grey;
$invalid-background-color: #ff000026;
$invalid-border-color: #ff00003b;

$padding-input: 0.5em;
$padding-half: 0.25em;
$padding-wrapper: 1.25em;
$padding-label: $padding-input + $padding-wrapper;

// Reset
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}

.putsy {
  // Outer bounding box
  &__outer {
    text-align: left;
    position: relative;
    margin-bottom: 1em;
    border-left: 4px solid $color-grey-2;
    cursor: poiner;
    transition: all 0.125s ease;

    &.invalid {
      border-left: 4px solid $invalid-border-color;
      background-color: $invalid-background-color;

      .input__inner::before {
        background-color: $invalid-background-color;
      }
    }

    &.focus {
      border-left: 4px solid #000;
    }

    &.disabled {
      opacity: 0.4;
      pointer-events: none;
    }

    &.valid .input__inner {
      color: #089e08;
    }
  }

  &__field {
    border: none;
    padding: $padding-input;
    background-color: transparent;
    padding: 0;
    position: relative;
    font-size: inherit;
    text-align: inherit;
    text-transform: inherit;
    font-weight: inherit;
    line-height: inherit;
    color: inherit;
    flex: 1;

    &:focus {
      outline: none;
    }

    &.invalid {
      color: red;
      animation: error 0.3s;
    }
  }

  &__label {
    width: 100%;
    text-align: inherit;
    padding-left: $padding-input;
    pointer-events: none;
    font-weight: 700;
    z-index: 1;
    transition: all 0.2s ease;
    position: relative;

    // &.dirty {
    //   top: 0.1em;
    //   // transform: scale(0.75);
    //   font-size: 0.9em;
    //   color: $color-samsung-blue;
    // }
  }

  &__fieldwrap {
    position: relative;
    // padding-top: 2.2em;
    padding: $padding-input $padding-half;
    display: flex;
    align-items: center;

    &::before {
      content: "";
      position: absolute;
      left: 0;
      right: 0;
      bottom: 0;
      height: 2px;
      background-color: $color-grey-2;
    }

    // On focus
    &::after {
      content: "";
      position: absolute;
      margin-left: auto;
      margin-right: auto;
      left: 0;
      width: 0;
      bottom: 0;
      height: 2px;
      transition: all 0.25s ease;
      background-color: #000;
    }

    * {
      padding: 0 $padding-half;
    }

    &.focus::after {
      width: 100%;
    }
  }

  &__appendix {
    // position: absolute;
    // right: 0;
    // top: 0;
  }

  &__counter {
    text-align: right;
    text-transform: uppercase;
    font-size: 0.9rem;
    font-weight: 700;
    padding: $padding-half $padding-input;
    color: $color-grey-2;
  }
} // Putsy closing tag

.required {
  color: red;
}

@keyframes error {
  8%,
  41% {
    -webkit-transform: translateX(-5px);
  }
  25%,
  58% {
    -webkit-transform: translateX(5px);
  }
  75% {
    -webkit-transform: translateX(-2.5px);
  }
  92% {
    -webkit-transform: translateX(2.5px);
  }
  0%,
  100% {
    -webkit-transform: translateX(0);
  }
}
</style>
