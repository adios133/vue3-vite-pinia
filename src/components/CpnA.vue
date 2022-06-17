<script setup>
import { useList } from '../store';
const list = useList();
</script>

<template>
  <div class="cpn-a">
    <div>组件A</div>
    <div class="list">
      <div>列表</div>
      <div class="item" v-for="item in list.list">
        {{ item.name }} - {{ item.age }} - {{ item.gender === 1 ? '男' : '女' }}
      </div>
    </div>
    <button>click</button>
  </div>
</template>

<style scoped>
.cpn-a {
  height: 300px;
  background-color: skyblue;
}
</style>
