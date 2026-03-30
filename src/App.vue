un<template>
  <el-config-provider :locale="locale">
    <router-view></router-view>
  </el-config-provider>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
  name: 'App',
  data() {
    return {
      locale: {
        name: this.$i18n.locale,
        el: this.$i18n.messages[this.$i18n.locale].el
      }
    }
  },
  watch: {
    '$i18n.locale'(val) {
      this.locale = {
        name: val,
        el: this.$i18n.messages[val].el
      }
    }
  }
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100%;
  height: 100vh;
}
</style>
