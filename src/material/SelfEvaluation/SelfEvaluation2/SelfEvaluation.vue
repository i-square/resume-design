<!-- 自我评价 -->
<template>
  <div class="self-eavluation">
    <!-- 标题 -->
    <model-title :title="modelData.title" :iconfont="modelData.iconfont" :model-style="modelStyle"></model-title>
    <!-- 兴趣爱好 -->
    <div class="self-eavluation-content">
      <p>{{ modelData.content }}</p>
    </div>
  </div>
</template>
<script setup lang="ts">
  import { ISELFEVALUATION } from '@/interface/model';
  import IMODELSTYLE from '@/interface/modelStyle';
  import ModelTitle from '@/material/ModelTitle/ModelTitle2/ModelTitle.vue';
  defineProps<{
    modelData: ISELFEVALUATION;
    modelStyle: IMODELSTYLE; // 模块样式
  }>();
</script>
<style lang="scss" scoped>
  .self-eavluation {
    padding-top: v-bind('modelStyle.pTop');
    padding-bottom: v-bind('modelStyle.pBottom');
    padding-left: v-bind('modelStyle.pLeftRight');
    padding-right: v-bind('modelStyle.pLeftRight');
    margin-bottom: v-bind('modelStyle.mBottom');
    margin-top: v-bind('modelStyle.mTop');
    box-sizing: border-box;
    .self-eavluation-content {
      display: flex;
      flex-direction: column;
      display: flex;
      width: 100%;
      flex-direction: column;
      margin-top: 20px;
      p {
        letter-spacing: 2px;
        text-align: justify;
        font-size: v-bind('modelStyle.textFontSize');
        color: v-bind('modelStyle.textColor');
        font-weight: v-bind('modelStyle.textFontWeight');
        line-height: 1.5;
      }
    }
  }
</style>
