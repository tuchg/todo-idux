<template>
  <IxSpace vertical>
    <IxSpace size="sm">
      <IxInput v-model:value="inputContent" @keydown="onEnter" />
      <IxButton icon="plus-circle" @click="handleAdd">Add</IxButton>
    </IxSpace>
    <IxTable :columns="columns" :dataSource="data" headless>
      <template #progress="{ record }">
        <IxButton
          @click="handleDone(record.key)"
          shape="circle"
          :mode="record.status === Status.isDone ? 'primary' : 'default'"
          icon="success"
        />
      </template>
      <template #action="{ record }">
        <div
          class="block"
          @mouseover="hoverIndex(record.key)"
          @mouseout="shouldShow = -1"
        >
          <IxButton
            v-show="shouldShow === record.key"
            mode="dashed"
            @click="handleDel(record.key)"
            danger
            icon="close"
            shape="circle"
          />
        </div>
      </template>
    </IxTable>
  </IxSpace>
</template>

<script setup lang="ts">
import { TableColumn } from "@idux/components";
import { ref } from "vue";

enum Status {
  isDone,
  inProgress,
}
interface Data {
  key?: number;
  content: string;
  status: Status;
}
let tableIndex: number = 0;

// 表格数据源
const data = ref<Data[]>([
  {
    key: tableIndex,
    content: "我是测试TODO数据",
    status: Status.inProgress,
  },
]);
const shouldShow = ref(-1);
// 输入内容
const inputContent = ref("");
/**
 * Add
 */
const handleAdd = () => {
  data.value.push({
    key: ++tableIndex,
    content: inputContent.value,
    status: Status.inProgress,
  });
};
/**
 * 输入框回车提交结果
 * @param evt
 */
const onEnter = (evt: KeyboardEvent) => (evt.keyCode === 13 ? handleAdd() : "");
/**
 * 删除按钮的显隐
 */
const hoverIndex = (index: number) => (shouldShow.value = index);

/**
 * Todo完成任务处理
 */
const handleDone = (key: number) => {
  data.value = data.value.map((v) => {
    if (v.key === key) {
      v.status = Status.isDone;
    }
    return v;
  });
};
/**
 * Todo任务删除处理
 * @param key 任务Key
 */
const handleDel = (key: number) => {
  data.value = data.value.filter((_v) => _v.key !== key);
};
/**
 * 表格数据列定义
 */
const columns: TableColumn<Data>[] = [
  {
    dataKey: "key",
    customCell: "progress",
  },
  {
    dataKey: "content",
    customCell: "",
  },
  {
    dataKey: "key",
    customCell: "action",
  },
];
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.block {
  width: 30px;
  height: 30px;
}
</style>
