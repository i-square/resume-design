<template>
  <el-dialog
    :model-value="dialogVisible"
    width="60%"
    :show-close="false"
    :close-on-click-modal="false"
    append-to-body
    custom-class="import-json"
  >
    <!-- 头部 -->
    <template #title>
      <div class="header">
        <div class="header-left">
          <h1 class="title">请在编辑器内输入简历JSON数据，格式如下：</h1>
          <span>
            <svg-icon icon-name="icon-xianshi_jinggao" color="red" size="14px"></svg-icon>
            注意：JSON数据通常为自定义模板时导出的JSON数据！
          </span>
        </div>
        <div class="header-right">
          <el-tooltip class="box-item" effect="dark" content="JSON示例" placement="bottom">
            <div class="icon-box" @click="openTip">
              <svg-icon icon-name="icon-daimashili" color="#fff" size="17px"></svg-icon>
            </div>
          </el-tooltip>
          <el-tooltip class="box-item" effect="dark" content="重置" placement="bottom">
            <div class="icon-box" @click="resetJson">
              <svg-icon icon-name="icon-zhongzhi" color="#fff" size="17px"></svg-icon>
            </div>
          </el-tooltip>
        </div>
      </div>
    </template>
    <!-- 代码编辑器 -->
    <div class="code-mirror-box">
      <codemirror
        v-model="code"
        placeholder="请将你在自定义模板下载的JSON数据粘贴在此处哦~~"
        :style="{ height: '400px' }"
        :autofocus="true"
        :indent-with-tab="true"
        :tab-size="2"
        :extensions="extensions"
      />
    </div>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="cancle">取消</el-button>
        <el-button type="primary" @click="confirmJson">确定</el-button>
      </span>
    </template>
  </el-dialog>

  <!-- JSON提示弹窗 -->
  <tip-json-dialog
    :tip-dialog-visible="tipDialogVisible"
    @close-tip-dialog="closeTipDialog"
  ></tip-json-dialog>
</template>

<script lang="ts" setup>
  import { Codemirror } from 'vue-codemirror';
  import { javascript } from '@codemirror/lang-javascript';
  import { oneDark } from '@codemirror/theme-one-dark';
  import { json } from '@codemirror/lang-json';
  import { ref } from 'vue';
  import RESUME_JSON from '@/schema/resume';
  import IMPORT_JSON from '@/schema/import';
  import { ElMessage } from 'element-plus';
  import { isJSON } from '@/utils/common';
  import { useRoute } from 'vue-router';
  import appStore from '@/store';
  import TipJsonDialog from '@/components/TipJsonDialog/TipJsonDialog.vue';

  const emit = defineEmits(['cancle']);

  interface TDialog {
    dialogVisible: boolean;
  }
  withDefaults(defineProps<TDialog>(), {
    dialogVisible: false
  });

  // 代码编辑器
  const code = ref<string>('');
  const extensions = [javascript(), oneDark, json()];

  // 取消
  const cancle = () => {
    emit('cancle');
  };

  // 提示弹窗
  const tipDialogVisible = ref<boolean>(false);
  const openTip = () => {
    tipDialogVisible.value = true;
  };
  // 关闭提示弹窗
  const closeTipDialog = () => {
    tipDialogVisible.value = false;
  };

  // 重置
  const resetJson = () => {
    code.value = JSON.stringify(IMPORT_JSON, null, 4);
    ElMessage({
      message: '重置成功！',
      type: 'success',
      center: true
    });
  };

  // 提交JSON
  const { setUuid } = appStore.useUuidStore;
  const route = useRoute();
  const { changeResumeJsonData, changeImportJsonData } = appStore.useResumeJsonNewStore;
  const { resetSelectModel } = appStore.useSelectMaterialStore;
  const confirmJson = () => {
    if (!code.value) {
      ElMessage({
        message: '数据不能为空！',
        type: 'error',
        center: true
      });
      return;
    }
    let isCurrentJSON = isJSON(code.value);
    if (!isCurrentJSON) {
      ElMessage({
        message: 'JSON格式不正确！',
        type: 'error',
        center: true
      });
      return;
    }
    // // 处理数据
    let importJson = JSON.parse(code.value);
    importJson.ID = 4;
    console.log('导入的最终JSON', importJson);
    changeResumeJsonData(importJson); // 更改store的数据
    changeImportJsonData(importJson); // 保存JSON数据
    setUuid(); // 重新渲染左侧列表和右侧属性面板设置
    resetSelectModel(); // 重置选中模块
    emit('cancle');
  };
</script>
<style lang="scss">
  .import-json {
    .header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      .header-left {
        display: flex;
        flex-direction: column;
        height: 48px;
        justify-content: space-between;
        .title {
          font-size: 20px;
        }
        span {
          font-size: 12px;
          color: red;
          display: flex;
          align-items: center;
          .svg-icon {
            margin-right: 5px;
          }
        }
      }
      .header-right {
        display: flex;
        .icon-box {
          width: 35px;
          height: 35px;
          background-color: #74a274;
          border-radius: 50%;
          display: flex;
          align-items: center;
          justify-content: center;
          cursor: pointer;
          margin-left: 15px;
          transition: all 0.3s;
          &:hover {
            background-color: rgba(0, 192, 145, 0.8);
          }
        }
      }
    }
    .el-dialog__header {
      padding: 20px;
    }
    .el-dialog__body {
      padding: 0;
    }
    .el-dialog__footer {
      padding: 20px;
    }
  }

  .cm-editor {
    height: 70vh;
  }
  .el-overlay-dialog {
    display: flex;
    justify-content: center;
    align-items: center;
    .el-dialog {
      padding: 0;
      margin: 0;
    }
  }
</style>
