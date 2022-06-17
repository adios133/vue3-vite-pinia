<script setup>
import { useList } from '../store';
const list = useList();
const add = () => {
  // list.counts++
  list.addCount('777')
  // list.$patch({ counts: list.counts + 1 })
};
const reset = () => {
  list.$reset()
}
</script>

<template>
  <div class="cpn-a">
    <div>组件A</div>
    <div class="list">
      <div>state列表</div>
      <div class="item" v-for="item in list.list">
        {{ item.name }} - {{ item.age }} - {{ item.gender === 1 ? '男' : '女' }}
      </div>
      <div>getters列表</div>
      <div class="item" v-for="item in list.listFiltered">
        {{ item.name }} - {{ item.age }} - {{ item.gender === 1 ? '男' : '女' }}
      </div>
      <div>getters</div>
      <div>{{list.doubleCount}}</div>
      <div>{{list.sixTimeCount}}</div>
    </div>
    <div style="color:red">List.count:{{list.counts}}</div>
    <button @click="add">click</button>
    <button @click="reset">reset</button>
  </div>
</template>

<style scoped>
.cpn-a {
  height: 300px;
  background-color: skyblue;
}
</style>
