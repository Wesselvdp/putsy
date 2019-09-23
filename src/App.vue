<template>
  <div id="app">
    <Base-input appendix="span" v-model="name" label="label" placeholder="placeholder" value="Lor" />
    <Base-Textarea label="label" placeholder="placeholder" value="Lorem Ipsum domitat solus" />
    <Base-Search  :options="options" />

    <Base-Select />
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: '',
      options: ['option 1', 'option 2', 'option 3'],
    };
  },
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
