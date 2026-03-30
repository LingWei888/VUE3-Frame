<template>
  <div class="layout-container">
    <el-alert type="success">
      <p style="text-align: left;">当前示例已调整为模板内置拖拽分栏演示布局。</p>
      <p style="text-align: left;">如需更复杂的拖拽分栏，建议后续单独升级对应组件库后再接入。</p>
    </el-alert>
    <div class="pane-layout">
      <div class="pane-layout__left">
        <div class="item item--orange">1</div>
      </div>
      <div class="pane-layout__center">
        <div class="item item--yellow">2</div>
        <div class="item item--lime">3</div>
        <div class="item item--green">4</div>
      </div>
      <div class="pane-layout__right">
        <div class="item item--blue">5</div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
  setup() {}
})
</script>

<style lang="scss" scoped>
  .pane-layout {
    display: grid;
    grid-template-columns: minmax(180px, 1fr) minmax(320px, 1.6fr) minmax(180px, 1fr);
    gap: 16px;
    height: calc(100vh - 260px);
    min-height: 520px;
    margin-top: 16px;

    &__left,
    &__right {
      height: 100%;
    }

    &__center {
      display: grid;
      grid-template-rows: repeat(3, 1fr);
      gap: 16px;
      height: 100%;
    }
  }

  .item {
    display: flex;
    justify-content: center;
    align-items: center;
    font-family: Helvetica, Arial, sans-serif;
    color: #333;
    font-size: 5em;
    width: 100%;
    height: 100%;
    border-radius: 10px;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.08);
  }

  .item--orange {
    background-color: #fe5308;
  }

  .item--yellow {
    background-color: #fefe34;
  }

  .item--lime {
    background-color: #cfea2b;
  }

  .item--green {
    background-color: #66b032;
  }

  .item--blue {
    background-color: #0492ce;
  }

  @media (max-width: 992px) {
    .pane-layout {
      grid-template-columns: 1fr;
      height: auto;
      min-height: auto;

      &__left,
      &__right {
        height: 180px;
      }

      &__center {
        grid-template-rows: repeat(3, 180px);
      }
    }
  }
</style>