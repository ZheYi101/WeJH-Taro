<template>
  <w-list class="dark-mode-toggle" style="border-radius: 8px;">
    <w-list-item arrow="right" @tap="handleAdaptToggle">
      <view class="text-wrapper">
        <text> 自动设置 </text>
        <text class="state">
          {{ optionText }}
        </text>
      </view>
    </w-list-item>
    <w-list-item v-if="!isAdapted">
      <view class="text-wrapper">
        <text>深色模式</text>
        <w-swtich
          v-model="isActive"
        />
      </view>
    </w-list-item>
  </w-list>
</template>

<script setup lang="ts">
import { WList, WListItem, WSwtich } from "@/components";
import { useDarkMode } from "@/hooks";
import Taro from "@tarojs/taro";
import { computed, ref, watch } from "vue";
const optionValueMap = {
  "adapted": "跟随微信",
  "noAdapted": "手动设置"
};

const { isAdapted, setIsAdapted, setMode, mode } = useDarkMode();

const isActive = ref(mode.value === "light");

watch(isActive, () => {
  if (!isAdapted.value) handleDarkToggle();
});

const optionText = computed(() => {
  if (isAdapted.value) return optionValueMap["adapted"];
  else return optionValueMap["noAdapted"];
});

const handleAdaptToggle = () => {
  Taro.showActionSheet({
    itemList: Object.values(optionValueMap),
    success: async (e) => {
      if (e.tapIndex === 0) {
        await setIsAdapted(true);
        isActive.value = mode.value === "light";
      } else {
        isActive.value = mode.value === "light";
        await setIsAdapted(false);
      }
    }
  });
};

const handleDarkToggle = () => {
  setMode(mode.value === "light" ? "dark" : "light");
  setIsAdapted(false);
};
</script>

<style lang="scss">
.dark-mode-toggle {

  .text-wrapper {
    display: flex;
    flex: auto;
    justify-content: space-between;
    align-items: center;
  }

  .state {
    color: var(--wjh-color-week);
  }
}
</style>
