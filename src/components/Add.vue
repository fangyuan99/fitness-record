<template>
  <div class="addItem">
    <el-collapse v-model="activeNames" @change="handleChange">
      <el-collapse-item name="1">
        <template #title>
          <el-date-picker
            v-model="date"
            type="date"
            placeholder="Pick a Date"
            format="YYYY/MM/DD"
            value-format="YYYY-MM-DD"
          />
        </template>
        <el-table v-model:data="todayData" style="width: 100%">
          <el-table-column prop="name" label="训练项目" sortable />
          <el-table-column prop="count" label="次数" />
          <el-table-column prop="weight" label="重量(kg)" />
          <el-table-column prop="nums" label="组数" />
        </el-table>
        <div>
          <el-row style="padding: 5px ;">
            <el-col :span="12">
              <el-select-v2
                v-model="value"
                :options="options"
                placeholder="训练项目"
                style="width: 87%; vertical-align: middle"
                allow-create
                filterable
                clearable
            /></el-col>
            <el-col :span="12"
              ><el-input-number v-model="num[0]" :min="0"
            /></el-col>
          </el-row>
          <el-row>
            <el-col :span="12"
              ><el-input-number v-model="num[1]" :step="2.5" :min="0"
            /></el-col>
            <el-col :span="12">
              <el-input-number v-model="num[2]" :min="0"
            /></el-col>
          </el-row>

          <el-button type="primary" @click="addItem()" style="margin: 10px ;"
            >添加
            <i inline-flex i="ep-plus" />
          </el-button>
        </div>
      </el-collapse-item>
    </el-collapse>
  </div>
</template>

<script lang="ts" setup>
import { ref, reactive, watch, defineEmits } from "vue";
import { ElMessage } from "element-plus";

//接受从父组件传递过来的数据currentPage,data
const props = defineProps<{
  data: any;
}>();

const addItem = function () {
  console.log("addItem");
  if (value.value == "选择训练项目") {
    ElMessage({
      message: "请选择训练项目",
      type: "error",
    });
    return;
  }
  //若有负值，提示错误
  if (num.value[0] <= 0 || num.value[1] <= 0 || num.value[2] <= 0) {
    ElMessage({
      message: "你还能再少点吗？",
      type: "error",
    });
    return;
  }
  todayData.push({
    name: value.value,
    count: num.value[0],
    weight: num.value[1],
    nums: num.value[2],
  } as never);
};

const getDate = function () {
  const date = new Date();
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();
  return year + "-" + month + "-" + day;
};

let date = ref(getDate());

let num = ref([10, 5, 3]);

let value = ref("选择训练项目");

const options = [
  {
    value: "杠铃卧推",
    label: "杠铃卧推",
  },
  {
    value: "杠铃划船",
    label: "杠铃划船",
  },
  {
    value: "哑铃弯举",
    label: "哑铃弯举",
  },
  {
    value: "哑铃推举",
    label: "哑铃推举",
  },
  {
    value: "杠铃深蹲",
    label: "杠铃深蹲",
  },
];

//若本地存储中有今天的数据，则转为reactive读取
let todayData = reactive(
  JSON.parse(localStorage.getItem(getDate()) as string) || []
);

const emit = defineEmits(["save"]);

//监听todayData的变化，若有变化则保存到本地
watch(todayData, (val) => {
  //向父组件发送数据
  console.log("emit");
  //若val中有val.name,val.count,val.weight完全相同的数据，则合并
  let temp = val;
  for (let i = 0; i < temp.length; i++) {
    for (let j = i + 1; j < temp.length; j++) {
      if (
        temp[i].name == temp[j].name &&
        temp[i].count == temp[j].count &&
        temp[i].weight == temp[j].weight
      ) {
        temp[i].nums += temp[j].nums;
        temp.splice(j, 1);
        j--;
      }
    }
  }
  emit("save", {
    date: date.value,
    trainingContent: val,
  });
  localStorage.setItem(getDate(), JSON.stringify(val));
});

watch(date, (val) => {
  //若props.data中有当前日期数据，则读取

});

const activeNames = ref(["1"]);
const handleChange = (val: string[]) => {
  console.log(val);
};
</script>

<style scoped>
.addItem {
  /* 添加训练项目模块
   */
  margin: 20px;
  /* 背景 */
  /* background-color: #f5f5f5; */
  /* 边框 */
  border: 1px solid #ebeef5;
  /* 圆角 */
  border-radius: 4px;
  /* 阴影 */
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}
</style>
