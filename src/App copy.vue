<script setup>
import { ref } from "vue";

const items = ref([
  { id: 1, title: "Item 1.1", list: 1 },
  { id: 2, title: "Item 1.2", list: 1 },
  { id: 3, title: "Item 2.1", list: 2 },
]);

const getList = (list) => {
  return items.value.filter((l) => l.list === list);
};

const startDrag = (event, item) => {
  console.log(item);
  event.dataTransfer.dropEffect = "move";
  event.dataTransfer.effectAllowed = "move";
  event.dataTransfer.setData("itemID", item.id);
};

const onDrop = (event, list) => {
  const itemID = event.dataTransfer.getData("itemID");
  console.log("itemID :>> ", itemID);
  console.log(event, list);
  const item = items.value.find((i) => i.id == itemID);
  console.log("item :>> ", item);
  item.list = list;
};
</script>

<template>
  <div
    class="drop-zone"
    @drop="onDrop($event, 1)"
    @dragenter.prevent
    @dragover.prevent
  >
    <div
      v-for="item in getList(1)"
      :key="item.id"
      class="drag-el"
      draggable="true"
      @dragstart="startDrag($event, item)"
    >
      {{ item.title }}
    </div>
  </div>

  <div
    class="drop-zone"
    @drop="onDrop($event, 2)"
    @dragenter.prevent
    @dragover.prevent
  >
    <div
      v-for="item in getList(2)"
      :key="item.id"
      class="drag-el"
      draggable="true"
      @dragstart="startDrag($event, item)"
    >
      {{ item.title }}
    </div>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.drop-zone {
  width: 50%;
  margin: 50px auto;
  background-color: #ecf0f1;
  padding: 10px;
  min-height: 60px;
}

.drag-el {
  background-color: #3498db;
  color: white;
  padding: 5px;
  margin: 10px;
}
</style>
