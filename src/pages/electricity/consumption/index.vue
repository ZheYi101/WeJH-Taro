<template>
  <theme-config>
    <title-bar title="用电记录" back-button />
    <card class="consumption-card">
      <scroll-view :scroll-y="true">
        <view class="container">
          <card v-if="!loading && !consumptionList?.data.length" class="no-item">
            无用电记录
          </card>
          <template v-else>
            <list
              v-for="consumption in consumptionList?.data"
              :key="consumption.room_dm"
            >
              <list-item class="consumption-list-item">
                <view class="text-wrapper">
                  <text> {{ consumption.datetime }} </text>
                  <text> {{ consumption.used }} </text>
                </view>
              </list-item>
            </list>
          </template>
          <text v-if="loading" class="load">
            正在加载中...
          </text>
        </view>
      </scroll-view>
    </card>
  </theme-config>
</template>

<script setup lang="ts">
import "./index.scss";
import { Card, ThemeConfig, TitleBar } from "@/components";
import { useRequest } from "@/hooks";
import { YxyService } from "@/services";
import List from "../../../components/List/List.vue";
import ListItem from "../../../components/List/ListItem.vue";
import { serviceStore } from "@/store";

const {
  data: consumptionList,
  loading
} = useRequest(YxyService.queryConsumption, {
  defaultParams: {
    campus: serviceStore.electricity.electricityCampus
  },
  onSuccess: (response) => {
    if (response.data.code !== 1) {
      throw new Error(response.data.msg);
    }
  },
  onError: (error) => {
    if (error instanceof Error) {
      return error.message;
    } else return `查询用电记录失败\r\n${error.errMsg}`;
  }
});

</script>
