<template>
  <div class="train-list">
    <el-table :data="data1" :style="st">
      <el-table-column prop="name" label="训练项目" :style="st" sortable />
      <el-table-column prop="count" label="次数" >
        <template #default="scope" >
          <div :style="st">
            {{scope.row.count}}
          </div>
        </template>
      </el-table-column>
      <el-table-column prop="weight" label="重量(kg)" />
      <el-table-column prop="nums" label="组数" />
    </el-table>
  </div>
  <div class="train-list">
    <el-table :data="data2" :style="st">
      <el-table-column prop="name" label="训练项目" sortable />
      <el-table-column prop="count" label="次数" />
      <el-table-column prop="weight" label="重量(kg)" />
      <el-table-column prop="nums" label="组数" />
    </el-table>
  </div>
</template>

<script lang="ts" setup>
import { computed } from "vue";
//接受从父组件传递过来的数据currentPage,data
const props = defineProps<{
  data1: any;
  data2: any;
}>();

const st = computed(() => {
  return `--bgColor: yellow;background-color: var(--bgColor);`;
});

const data1 = computed(() => {
  if (props.data1 == null) {
    return [];
  }
  //props.data按data.trainingContent.name字典序排序
  props.data1.trainingContent.sort((a: any, b: any) => {
    return a.name.localeCompare(b.name);
  });
  return props.data1.trainingContent;
});

const data2 = computed(() => {
  if (props.data2 == null) {
    return [];
  }
  //props.data按data.trainingContent.name字典序排序
  props.data2.trainingContent.sort((a: any, b: any) => {
    return a.name.localeCompare(b.name);
  });
  return props.data2.trainingContent;
});
</script>
<style scoped>
.train-list {
  padding: 10px;
  margin: 10px;
  /* 背景 */
  /* background-color: #fff; */
  /* 边框 */
  border: 1px solid #ebeef5;
  /* 圆角 */
  border-radius: 4px;
  /* 阴影 */
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}
.bg{
  background-color: var(--bgColor);
}
</style>
