<script setup>
import { ref } from "vue";

const items = ref([
  { id: 1, title: "Ahmet 1.1.1", list: 1, order: 1 },
  { id: 2, title: "Mehmet 1.2", list: 1, order: 2 },
  { id: 3, title: "Felo 1.3", list: 1, order: 3 },
  { id: 4, title: "İfo 1.4", list: 1, order: 4 },
  { id: 5, title: "Selo 1.5", list: 1, order: 5 },
  { id: 6, title: "Cemo 2.1", list: 2, order: 1 },
]);

const getList = (list) => {
  return items.value
    .filter((l) => l.list === list)
    .sort((a, b) => a.order - b.order);
};

const startDrag = (event, item) => {
  // console.log(item);
  event.dataTransfer.dropEffect = "move";
  event.dataTransfer.effectAllowed = "move";
  event.dataTransfer.setData("itemID", item.id);
  event.dataTransfer.setData("item", JSON.stringify(item));
};

const onDrop = (event, list) => {
  console.log("Drop..");
  //! Herhangi bir item üzerine sürüklenmiş olabilir.
  if (!event.toElement.classList.contains("drag-el")) {
    const itemID = event.dataTransfer.getData("itemID");
    const item = items.value.find((i) => i.id == itemID);
    item.list = list;
    item.order = items.value.filter((i) => i.list == list).length;
  }
};

const onDrop2 = (event, droppedItem) => {
  console.log("Drop2..", event);
  //! üzerine birakilan item
  // console.log("droppedItem :>> ", droppedItem);
  //! Sürüklenen itemID
  const draggedItemID = event.dataTransfer.getData("itemID");
  const draggedItem = items.value.find((i) => i.id == draggedItemID);

  if (droppedItem.list !== draggedItem.list) {
    draggedItem.list = droppedItem.list;
    if (draggedItem.order === droppedItem.order)
      draggedItem.order = droppedItem.order - 1;
  }

  if (draggedItem.order < droppedItem.order) {
    if (droppedItem.order - draggedItem.order === 1) {
      draggedItem.order += 1;
      droppedItem.order -= 1;
    } else {
      draggedItem.order = droppedItem.order + 1;
      droppedItem.order--;
      let itemOrderCounter = 0;
      items.value = items.value
        .sort((a, b) => a.order - b.order)
        .map((i, j) => {
          if (i.list != droppedItem.list) return i;
          return {
            ...i,
            order: ++itemOrderCounter,
          };
        });
    }
  } else if (draggedItem.order > droppedItem.order) {
    if (draggedItem.order - droppedItem.order === 1) {
      draggedItem.order -= 1;
      droppedItem.order += 1;
    } else {
      draggedItem.order = droppedItem.order - 1;
      droppedItem.order++;
      let itemOrderCounter = 0;
      items.value = items.value
        .sort((a, b) => a.order - b.order)
        .map((i, j) => {
          if (i.list != droppedItem.list) return i;
          return {
            ...i,
            order: ++itemOrderCounter,
          };
        });
    }
  }
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
      @drop="onDrop2($event, item)"
    >
      {{ item.title }}
      - {{ item.order }}
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
      @drop="onDrop2($event, item)"
    >
      {{ item.title }} - {{ item.order }}
    </div>
  </div>

  <div class="result">
    <pre>
    {{ getList(1) }}
    </pre>
    <pre>
    {{ getList(2) }}
    </pre>
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

.result {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}
</style>
