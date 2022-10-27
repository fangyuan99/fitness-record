<template>
  <div class="train-list">
    <el-collapse
      v-model="activeNames"
      @change="handleChange"
      v-for="(d, index) in currentPageData"
    >
      <el-collapse-item :title="d.date + ' ' + getWeek(d.date)" :name="index">
        <el-table :data="d.trainingContent" style="width: 100%">
          <el-table-column prop="name" label="训练项目" sortable />
          <el-table-column prop="count" label="次数" />
          <el-table-column prop="weight" label="重量(kg)" />
          <el-table-column prop="nums" label="组数" />
        </el-table>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from "vue";

//接受从父组件传递过来的数据currentPage,data
const props = defineProps<{
  currentPage: number;
  pageSize: number;
  data: any;
}>();

const activeNames = ref(["1"]);
const handleChange = (val: string[]) => {
  console.log(val);
};

//定义一个方法根据日期获取星期几
const getWeek = (date: string) => {
  const week = new Date(date).getDay();
  switch (week) {
    case 0:
      return "星期日";
    case 1:
      return "星期一";
    case 2:
      return "星期二";
    case 3:
      return "星期三";
    case 4:
      return "星期四";
    case 5:
      return "星期五";
    case 6:
      return "星期六";
  }
};

//定义一个计算属性，根据当前页码和每页显示的条数，计算出当前页的数据
const currentPageData = computed(() => {
  const start = (props.currentPage - 1) * props.pageSize;
  const end = start + props.pageSize;
  return props.data.slice(start, end);
});
</script>
<style scoped>
.train-list {
  padding: 10px;
  margin: 20px;
  /* 边框 */
  border: 1px solid #ebeef5;
  /* 圆角 */
  border-radius: 4px;
  /* 阴影 */
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}
</style>
