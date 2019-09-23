<template>
  <div
    :class="['search__outer', {'light': light}, {'disabled': disabled}]">
    <!-- Label -->
    <label :class="['search__label', {'dirty': dirty}]">
      {{ label }}
    </label>
    <!-- inner -->
    <div :class="['search__inner', {'focus': focus}]">
      <!-- <span
        v-for="(tag, index) in tags"
        :key="index"
        class="search__tag">{{ tag }}</span> -->
      <!-- Input -->
      <input
        v-bind="$attrs"
        :label="label"
        :placeholder="placeholder"
        :class="['search__input', {'invalid' : error}]"
        :focus="focus"
        @focus="handleFocus($event.target.value);"
        @input="handleInput($event.target.value)"
        @blur="handleBlur();"
        @keydown.enter="handleEnter($event.target.value)"
        @keydown.down="down"
        @keydown.up="up">
      <!-- <span class="search__suggestion">
        {{ suggest }}
      </span> -->
      <!-- <ArrowDown class="search__icon" /> -->
    </div>
    <!-- Menu -->
    <ul
      :class="['search__menu', {'visible': isOpen}]">
      <li
        v-for="(result, index) in searchResults"
        :key="index"
        tab-index="0"
        :class="['search__option', {'highlighted' : highlightIndex === index}]"
        @mouseover="highlightIndex = index"
        @click="suggestionSelected(searchResults[highlightIndex])">
        {{ result }}
      </li>
    </ul>
    <!-- / Menu -->
  </div>
</template>
<script>
export default {
  name: 'BaseSearch',
  inheritAttrs: false,
  model: {
    event: 'update',
  },
  props: {
    options: {
      type: Array,
      default: Array,
    },
    suffix: {
      type: String,
      default: null,
    },
    label: {
      type: String,
      default: null,
    },
    placeholder: {
      type: String,
      default: null,
    },
    light: {
      type: Boolean,
      default: false,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    explicit: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      value: '',
      focus: false,
      error: false,
      filled: false,
      isOpen: false,
      highlightIndex: -1,
      tags: [
        'Nature', 'Mountains',
      ],
    };
  },
  computed: {
    dirty() {
      return this.focus || this.$attrs.value || this.value || this.placeholder;
    },

    searchResults() {
      const input = this.$attrs.value ? this.$attrs.value.toLowerCase() : null;
      // filter options on input
      const results = this.options.filter(option => option.toLowerCase().includes(input));

      if (!input) return this.options;

      return results;
    },

    // Suggestion rules
    beginsWith() {
      // does suggestion begin with search value?
      if (this.suggestion) {
        return this.suggestion.startsWith(this.$attrs.value);
      }
      return false;
    },

    isValid() {
      // If search value doesn't have to be within options, everything is okay
      if (this.explicit) return true;
      return this.options.includes(this.value);
    },

    suggest() {
      // Determine whetther or not to show suggestion
      if (this.beginsWith === true && this.focus === true) return this.suggestion;
      return '';
    },

    suggestion() {
      // Actual suggestion
      return this.searchResults[this.highlightIndex];
    },
  },

  watch: {
    focus() {
      if (this.focus === false) {
        this.setOpen(false);
      }
    },
  },

  mounted() {
    document.addEventListener('keyup', this.nextItem);
  },

  destroyed() {
    document.removeEventListener('keyup', this.nextItem);
  },

  methods: {
    // native events
    handleFocus(value) {
      this.focus = true;
      this.$scrollTo('#bookingApp', 300, {
        offset: 9999,
      });
      this.evaluateOpen(value);
    },
    handleBlur() {
      this.focus = false;
      // this.setOpen(false);
    },
    handleInput(val) {
      this.update(val);
      this.evaluateOpen(val);
    },
    update(val) {
      this.$emit('update', val);
    },

    // Open / Close
    setOpen(isOpen) {
      this.isOpen = isOpen;
      if (isOpen) this.highlightIndex = -1;
    },

    toggleOpen() {
      this.isOpen = !this.isOpen;
    },

    evaluateOpen(value) {
      // only open for more than 2 results or when value isn't equal
      if (this.searchResults.length > 1 || value !== this.searchResults[0]) {
        this.setOpen(true);
      } else {
        this.setOpen(false);
      }
    },

    setFocus() {
      this.focus = true;
    },

    // Handle option slection thtough click or enter
    suggestionSelected(option) {
      if (option) this.update(option);
      this.isOpen = false;
    },

    // Optional, give error when needed
    giveError() {
      this.error = true;
      setTimeout(() => (this.error = false), 1000);
    },

    // Navigate list
    down() {
      if (this.highlightIndex < this.searchResults.length - 1) {
        this.highlightIndex += 1;
      }
    },
    up() {
      if (this.highlightIndex >= 0) this.highlightIndex -= 1;
    },
    handleEnter(value) {
      // When user uses custom search input
      if (this.highlightIndex === -1 && !this.isValid) this.giveError();
      // When user uses navigation in search options
      const result = this.searchResults[this.highlightIndex];
      if (result) this.update(result);
      this.tags.push(result);
      this.setOpen(false);
    },
  },
};
</script>

<style lang="scss">
$color-samsung-blue: blue;
$color-grey-1: grey;
$color-grey-2: grey;
$padding-input: 0.5em;
$padding-wrapper: 1.25em;
$padding-label: $padding-input + $padding-wrapper;

.search {
  &__outer {
    position: relative;
    margin-bottom: 1em;
    padding-top: $padding-wrapper;
    cursor: pointer;

    &.light {
      .search {
        &__inner {
          &::before {
            background-color: rgba(255, 255, 255, 0.767);
          }

          // On focus
          &::after {
            background-color: #fff;
          }
        }
      }
    }
    &.disabled {
      opacity: 0.4;
      pointer-events: none;
    }
  }

  &__tag {
    background-color: $color-samsung-blue;
    border-radius: 1em;
    display: flex;
    align-items: center;
    padding: 0 6px;
    margin: 0 4px;
    color: white;
  }

  &__suggestion {
    position: absolute;
    left: $padding-input;
    top: $padding-input;
    opacity: 0.3;
    z-index: 0;
  }

  &__inner {
    position: relative;
    display: flex;

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
      background-color: $color-samsung-blue;
    }

    &.focus::after {
      width: 100%;
    }
  }

  &__input {
    border: none;
    padding: $padding-input;
    background-color: transparent;
    position: relative;
    font-size: inherit;
    text-align: inherit;
    text-transform: inherit;
    font-weight: inherit;
    color: inherit;
    width: 100%;
    z-index: 1;

    &:focus {
      outline: none;
    }
    &.invalid {
      color: red;
      animation: error 0.3s;
    }
  }

  &__icon {
    position: absolute;
    right: 0;
  }

  &__placeholder {
    //
  }

  // Elements
  &__counter {
    text-align: right;
    text-transform: uppercase;
    font-size: 0.9rem;
    font-weight: 700;
    margin-top: 0.5em;
    color: $color-grey-2;
  }

  &__label {
    width: 100%;
    text-align: inherit;
    padding-left: $padding-input;
    pointer-events: none;
    position: absolute;
    z-index: 1;
    transition: all 0.2s ease;
    // top: calc(#{$padding-label} - 2px);

    // &.dirty {
    //   top: 0.1em;
    //   // transform: scale(0.75);
    //   font-size: 0.9em;
    //   color: $color-samsung-blue;
    // }
  }

  &__menu {
    position: absolute;
    width: 100%;
    visibility: hidden;
    z-index: 10;
    list-style: none;
    padding-left: 0;
    border: 1px solid $color-grey-2;
    box-shadow: 0 5px 15px -2px rgba(0, 0, 0, 0.3);
    opacity: 0;
    transform: translateY(5px);
    transition: all 0.3s ease-in-out;
    max-height: 16em;
    border-bottom-left-radius: 4px;
    border-bottom-right-radius: 4px;
    overflow: scroll;

    &.visible {
      transform: translateY(0);
      visibility: visible;
      opacity: 1;
    }
  }

  &__option {
    padding: 0.5em 1em;
    cursor: pointer;
    background-color: #fff;

    &:last-of-type {
      border-bottom-left-radius: 4px;
      border-bottom-right-radius: 4px;
    }

    &.highlighted {
      background-color: $color-grey-2;
      // color: $color-white;
    }
  }
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
