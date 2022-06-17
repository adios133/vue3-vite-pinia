<script setup>
import { useCount } from '../store';
import {storeToRefs} from 'pinia'
import { useList } from '../store';
const list = useList();
const counter = useCount()
const {count} = storeToRefs(counter)
const {addCount} = counter
const add = () => {
  addCount();
};
</script>

<template>
  <div class="cpn-b">
    <div>组件B</div>
    <div>Count.count:{{ count }}</div>
    <div style="color:red">List.count:{{ list.counts }}</div>
    <button @click="add">click</button>
  </div>
</template>

<style scoped>
.cpn-b {
  height: 300px;
  background-color: rgb(194, 140, 230);
}
</style>
