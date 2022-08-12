<template>
  <div class="list" @dragover="dragover" @drop="drop">
    <div class="item" draggable="true" v-for="(item, index) in itemList" :key="index"
      @dragstart="dragstart($event, index)" :data-index="index">
      {{ item }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const itemList = ref<number[]>([])

for (let i = 0; i < 24; i++) {
  itemList.value?.push(i)
}

function dragstart(e: DragEvent, index: number) {
  e.dataTransfer?.setData('dragIndex', String(index))
}

function dragover(e: DragEvent) {
  e.preventDefault()
}

function drop(e: DragEvent) {
  const targetDom = e.target as HTMLElement
  const dragIndex = Number((e.dataTransfer as DataTransfer).getData('dragIndex'))
  const targetIndexString = targetDom.dataset.index
  const targetIndex = Number(targetIndexString)
  if (!targetIndexString || (dragIndex === targetIndex)) return;
  const { clientX } = e
  const { left, width } = targetDom.getBoundingClientRect()
  if (clientX <= (left + width / 2)) {
    // before
    if (dragIndex < targetIndex) {
      itemList.value.splice(targetIndex, 0, itemList.value[dragIndex])
      itemList.value.splice(dragIndex, 1)
    }
    if (dragIndex > targetIndex) {
      itemList.value.splice(targetIndex, 0, itemList.value[dragIndex])
      itemList.value.splice(dragIndex + 1, 1)
    }
  } else {
    // after
    if (dragIndex < targetIndex) {
      itemList.value.splice(targetIndex + 1, 0, itemList.value[dragIndex])
      itemList.value.splice(dragIndex, 1)
    }
    if (dragIndex > targetIndex) {
      itemList.value.splice(targetIndex + 1, 0, itemList.value[dragIndex])
      itemList.value.splice(dragIndex + 1, 1)
    }
  }
}
</script>

<style scoped>
.list {
  height: 200px;
  display: grid;
  grid-template-columns: repeat(24, 1fr);
  column-gap: 10px;
}

.list .item {
  border: 1px solid black;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>