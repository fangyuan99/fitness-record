<template>
  <BaseHeader
    @output="output"
    @input="input"
    @dataComparison="dataComparison"
  />
  <h1>健身数据记录</h1>
  <DataContainer :currentPage="currentPage" :pageSize="pageSize" :data="data" />
  <Add @save="save" :data="data" />
  <table2Vue />
  <div class="demo-pagination-block">
    <el-pagination
      v-model:currentPage="currentPage"
      v-model:page-size="pageSize"
      :small="small"
      :background="background"
      :total="data.length"
      hide-on-single-page
      @current-change="handleCurrentChange"
    />

    <el-dialog
      v-model="centerDialogVisible"
      title="数据对比"
      style="height: 100%; width: 100%"
      center
    >
      <template #footer>
        <el-button>周数据对比</el-button>
        <!-- 将今天的数据和上周的数据进行对比 -->
        <div>
          <contrast :data1="weekData" :data2="findData(getDate())" />
        </div>
        <!-- <span class="dialog-footer">
          <el-button @click="centerDialogVisible = false">Cancel</el-button>
          <el-button type="primary" @click="centerDialogVisible = false">
            Confirm
          </el-button>
        </span> -->
      </template>
    </el-dialog>
  </div>
</template>
<script lang="ts" setup>
import { computed, reactive, ref, watch } from "vue";
import { ElMessage } from "element-plus";
//引入子组件
import table2Vue from "./table2.vue";

const getDate = function () {
  const date = new Date();
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();
  return year + "-" + month + "-" + day;
};

//计算属性weekData
const weekData = computed(() => {
  return findData(getWeekDate());
});

//获取一周前的日期
const getWeekDate = function () {
  const date = new Date(new Date().getTime() - 7 * 24 * 3600 * 1000);
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();
  return year + "-" + month + "-" + day;
};

const findData = (date) => {
  //在data中查找date的数据
  for (let i = 0; i < data.length; i++) {
    if (data[i].date == date) {
      return data[i];
    }
  }
  return null;
};

//定义output方法和input方法，导入导出data
const output = () => {
  console.log("output");
  const data1 = JSON.stringify(data);
  const blob = new Blob([data1], { type: "text/plain" });
  const url = URL.createObjectURL(blob);
  const link = document.createElement("a");
  link.href = url;
  link.download = getDate() + ".json";
  link.click();
  URL.revokeObjectURL(url);
};

//从本地文件导入数据
const input = () => {
  console.log("input");
  const input = document.createElement("input");
  input.type = "file";
  input.accept = ".json";
  input.onchange = (e) => {
    const file = (e.target as HTMLInputElement).files[0];
    const reader = new FileReader();
    reader.readAsText(file);
    reader.onload = (e) => {
      const data1 = JSON.parse(reader.result as string);
      //遍历data1，将数据添加到data中
      for (let i = 0; i < data1.length; i++) {
        data.push(data1[i]);
      }
    };
  };
  input.click();
};
const save = (val: any) => {
  //若val.date与data中每个元素的date都不同，说明是新增，否则是修改
  let flag = true;
  for (let i = 0; i < data.length; i++) {
    if (val.date == data[i].date) {
      flag = false;
      break;
    }
  }
  if (flag) {
    data.push(val as never);
    ElMessage({
      message: "添加成功",
      type: "success",
    });
  } else {
    for (let i = 0; i < data.length; i++) {
      if (val.date == data[i].date) {
        data[i] = val as never;
        break;
      }
    }
    ElMessage({
      message: "修改成功",
      type: "success",
    });
  }
  //对data进行排序，按照日期排序,typescript中的sort方法
  data.sort((a, b) => {
    return a.date > b.date ? 1 : -1;
  });
};
let centerDialogVisible = ref(false);

const dataComparison = () => {
  console.log("dataComparison");
  centerDialogVisible.value = true;
};

const currentPage = ref(1);
const pageSize = ref(10);
const small = ref(false);
const background = ref(false);
const disabled = ref(false);
let data = reactive(
  localStorage.getItem("data")
    ? JSON.parse(localStorage.getItem("data") as string)
    : []
);

//监听data的变化，若有变化则保存到本地
watch(data, (val) => {
  //向父组件发送数据
  console.log("emit");
  localStorage.setItem("data", JSON.stringify(val));
});

const handleSizeChange = (val: number) => {
  console.log(`${val} items per page`);
};
const handleCurrentChange = (val: number) => {
  console.log(`current page: ${val}`);
};
</script>

<style scoped>
.demo-pagination-block + .demo-pagination-block {
  margin-top: 10px;
}
.demo-pagination-block .demonstration {
  margin-bottom: 16px;
}
</style>
