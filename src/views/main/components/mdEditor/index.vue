<template>
  <div class="layout-container">
    <div class="layout-container-table">
      <v-md-editor v-model="text" height="60vh"></v-md-editor>
      <el-card class="box-card">
        <template #header>
          <p style="text-align: left;">
            v-model结果
            <el-button style="float: right; padding: 3px 0" type="text" @click="setData">初始赋值</el-button>
          </p>
        </template>
        <div>{{ text }}</div>
      </el-card>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
export default defineComponent({
  setup() {
    const text = ref('')
    function setData() {
      text.value = "# 我是一个标题"
    }
    return {
      text,
      setData
    }
  },
  mounted() {
    this.$nextTick(() => {
      
    })
  }
})
</script>

<style lang="scss" scoped>
  * {
    text-align: left;
  }
</style>