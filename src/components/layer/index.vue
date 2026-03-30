<template>
  <el-drawer
    ref="drawer"
    v-model="layer.show"
    :title="layer.title"
    :size="drawerSize"
    direction="rtl"
    :append-to-body="true"
    custom-class="system-layer-drawer"
  >
    <div class="system-layer-drawer__body system-scrollbar">
      <slot></slot>
    </div>
    <template #footer v-if="layer.showButton">
      <div class="system-layer-drawer__footer">
        <el-button @click="close">{{ $t('message.common.cancel') }}</el-button>
        <el-button type="primary" @click="confirm">{{ $t('message.common.confirm') }}</el-button>
      </div>
    </template>
  </el-drawer>
</template>

<script lang="ts">
import { computed, defineComponent, ref } from 'vue'
export interface LayerInterface {
  show: boolean;
  title: string;
  showButton?: boolean;
  width?: string;
  [propName: string]: any;
}
export interface LayerType {
  close: Function
}
export default defineComponent({
  props: {
    layer: {
      type: Object,
      default: () => {
        return {
          show: false,
          title: '',
          showButton: false
        }
      },
      required: true
    }
  },
  setup(props, ctx) {
    const drawer = ref(null)
    const drawerSize = computed(() => props.layer.width || '520px')
    function confirm() {
      ctx.emit('confirm')
    }
    function close() {
      props.layer.show = false
    }
    return {
      drawer,
      drawerSize,
      confirm,
      close
    }
  }
})
</script>

<style lang="scss" scoped>
  .system-layer-drawer {
    &__body {
      height: 100%;
      padding: 8px 8px 0;
      color: var(--system-page-color);
    }
    &__footer {
      display: flex;
      justify-content: flex-end;
      gap: 12px;
      padding-top: 12px;
    }
  }
</style>