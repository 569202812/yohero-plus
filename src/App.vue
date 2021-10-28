<template>
  <div>
    <Getting />
  </div>
</template>

<script lang="ts">
import { defineComponent, provide, ref, reactive } from "vue";
import Getting from "./components/Getting.vue";

export default defineComponent({
  name: "App",
  components: {
    Getting,
  },

  setup() {},
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
