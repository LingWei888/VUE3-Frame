<template>
  <el-dropdown @command="handleCommand" size="medium">
    <span class="el-dropdown-link">
      <i class="sfont system-wenzi"></i>
    </span>
    <template #dropdown>
      <el-dropdown-menu>
        <el-dropdown-item
          v-for="(locale, key) in $i18n.messages"
          :key="`locale-${locale.message.language}`"
          :command="key"
          :disabled="name === key"
        >
          {{ locale.message.language }}
        </el-dropdown-item>
      </el-dropdown-menu>
    </template>
  </el-dropdown>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import { useRoute } from 'vue-router'
import { useStore } from 'vuex'
import { changeTitle } from '@/utils/system/title'
export default defineComponent({
  data() {
    return {
      name: this.$i18n.locale
    }
  },
  methods: {
    handleCommand(command: string) {
      this.$i18n.locale = command
      this.$store.commit('app/stateChange', { name: 'lang', value: command })
      changeTitle(this.$route.meta.title)
      document.querySelector('html')!.setAttribute('lang', command)
      this.name = command
    }
  }
})
</script>

<style lang="scss" scoped>
  
</style>