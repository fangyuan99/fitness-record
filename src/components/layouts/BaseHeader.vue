<template>
  <el-menu class="el-menu-demo" mode="horizontal">
    <el-menu-item h="full" @click="toggleDark()">
      <button
        class="border-none w-full bg-transparent cursor-pointer"
        style="height: var(--ep-menu-item-height)"
      >
        <i inline-flex i="dark:ep-moon ep-sunny" />
      </button>
    </el-menu-item>
    <el-menu-item h="full" @click="dataComparison()">
      <button
        class="border-none w-full bg-transparent cursor-pointer"
        style="height: var(--ep-menu-item-height)"
      >
        <i inline-flex i="dark:ep-data-analysis ep-data-analysis" />
      </button>
    </el-menu-item>
    <el-menu-item h="full">
      <el-popconfirm
        @confirm="input"
        hide-icon
        confirm-button-text="确定"
        cancel-button-text="取消"
        title="确定从本地文件导入并覆盖数据?"
      >
        <template #reference>
          <button
            class="border-none w-full bg-transparent cursor-pointer"
            style="height: var(--ep-menu-item-height)"
          >
            导入
          </button>
        </template>
      </el-popconfirm>
    </el-menu-item>
    <el-menu-item h="full" @click="output()">
      <button
        class="border-none w-full bg-transparent cursor-pointer"
        style="height: var(--ep-menu-item-height)"
      >
        导出
      </button>
    </el-menu-item>
  </el-menu>
</template>
<script lang="ts" setup>
import { toggleDark } from "~/composables";

const emit = defineEmits(["input", "output", "dataComparison"]);

const input = () => {
  emit("input");
};

const output = () => {
  emit("output");
};

const dataComparison = () => {
  emit("dataComparison");
};
</script>
