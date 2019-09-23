<template>
  <div :class="['putsy putsy--textarea putsy__outer', {'focus': focus}, {'invalid': invalid}]">
    <div class="text__label">
      <label
        class="putsy__label"
        :for="name">
        {{ label }}
      </label>

    </div>

    <div class="putsy__fieldwrap">
      <textarea
      :id="name"
      v-bind="$attrs"
      :name="name"
      :focus="focus"
      :class="['putsy__field text', {'invalid' : error}]"
      :rows="4"
      :placeholder="label"
      @focus="focus = true"
      @blur="handleBlur"
      @input="$emit('update', $event.target.value)" />
    </div>
    <p class="text__counter">
      <!-- {{ length }} / 250 -->
    </p>
  </div>
</template>
<script>
export default {
  name: 'BaseTextarea',

  inheritAttrs: false,

  model: {
    event: 'update',
  },

  props: {
    invalid: {
      type: Boolean,
      default: false,
    },
    label: {
      type: String,
      default: null,
    },
    name: {
      type: String,
      default: null,
    },
  },

  data() {
    return {
      focus: false,
      error: false,
    };
  },

  computed: {
    // Counter that doesnt go lower than 0
    length() {
      const val = 250 - this.$attrs.value.length;
      return val < 0 ? 0 : val;
    },
  },

  methods: {
    handleBlur() {
      this.focus = false;
      if (this.length > 0) this.giveError();
    },

    // Optional, give error when needed
    giveError() {
      this.error = true;
      setTimeout(() => { (this.error = false); }, 1000);
    },
  },
};
</script>
