<template>
  <view class="applist-item" @tap="appTaped">
    <view class="icon-wrapper" :style="backgroundColor">
      <image
        v-if="isShowByUrl"
        :src="getIconUrl(icon,iconType)"
        :style="{opacity: iconOpacity}"
      />
      <view v-else :class="['iconfont', 'icon-'+icon]" />
    </view>
    <text class="label">
      {{ label }}
    </text>
  </view>
</template>

<script setup lang="ts">
import { serviceStore } from "@/store";
import Taro from "@tarojs/taro";
import { computed, ref, toRefs } from "vue";
import "./index.scss";
import { useDarkMode, useTheme } from "@/hooks";

const props = defineProps<{
  label: string,
  icon: string,
  url: string,
  bg: string,
  require: string,
}>();
const { require: requireActive, bg = ref("green"), label, url } = toRefs(props);

const { mode: darkMode } = useDarkMode();
const { isShowByUrl, getIconUrl, IconTypeEnum } = useTheme();

const icon = props.icon;

const iconType = computed(() => {
  if (darkMode.value === "light") return IconTypeEnum.applistIcon;
  else return isDisabled.value ? IconTypeEnum.quickviewIcon : IconTypeEnum.applistIcon;
});

const isDisabled = computed(() => {
  switch (requireActive.value) {
    case "zf":
      return !serviceStore.user.isBindZF && !serviceStore.user.isBindOauth;
    case "library":
      return !serviceStore.user.isBindLibrary;
    case "yxy":
      return !serviceStore.user.isBindYXY;
    default:
      return false;
  }
});

const iconOpacity = computed(() => {
  if (darkMode.value === "light") return undefined;
  else return isDisabled.value ? 0.1 : undefined;
});

async function appTaped() {
  if (isDisabled.value) {
    await Taro.navigateTo({ url: "/pages/bind/index" });
    Taro.showToast({
      icon: "none",
      title: "请绑定相关账号"
    });
  } else if (url.value) await Taro.navigateTo({ url: url.value });
}

const backgroundColor = computed(() => {
  if (isDisabled.value)
    return { "--bg-color": "var(--wjh-color-light)" };
  else return { "--bg-color": `var(--wjh-color-${bg.value}-600)` };
});

</script>
