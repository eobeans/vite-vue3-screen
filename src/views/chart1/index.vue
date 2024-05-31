<script setup lang="ts">
import CenterMap from "./center-map.vue";
import ScaleScreen from "@/components/scale-screen";
import { useSettingStore } from "@/stores/index";
import { storeToRefs } from "pinia";

const settingStore = useSettingStore();
const { isScale } = storeToRefs(settingStore);
const wrapperStyle = {};
</script>

<template>
  <scale-screen
    width="1920"
    height="1080"
    :delay="500"
    :fullScreen="false"
    :boxStyle="{
      background: '#03050C',
      overflow: isScale ? 'hidden' : 'auto',
    }"
    :wrapperStyle="wrapperStyle"
    :autoScale="isScale"
  >
    <div class="content_wrap">
      <div class="contetn_center">
        <CenterMap class="contetn_center_top" title="市场分布图" />
      </div>
    </div>
  </scale-screen>
</template>

<style scoped lang="scss">
.content_wrap {
  width: 100%;
  height: 100%;
  padding: 16px 16px 16px 16px;
  box-sizing: border-box;
  background-image: url("@/assets/img/other/img2/bg.png");
  background-size: cover;
  background-position: center center;
}
.contetn_center {
  flex: 1;
  margin: 0;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  min-height: calc(100% - 64px);
}
</style>
