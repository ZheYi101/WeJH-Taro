<template>
  <view :class="styles.alarm">
    <view :class="[styles['alarm-icon'], 'iconfont', 'icon-alarm']" />

    <view
      v-if="counter != 0"
      :class="styles['badge-wrapper']"
    >
      <w-badge size="small">
        <text>{{ counter.toString() }}</text>
      </w-badge>
    </view>
  </view>
</template>

<script setup lang="ts">
import { toRefs } from "vue";
import { WBadge } from "..";
import styles from "./index.module.scss";

const props = defineProps<{ counter: number }>();
const { counter } = toRefs(props);

</script>
