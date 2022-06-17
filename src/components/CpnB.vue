<script setup>
import { useCount } from '../store';
const { count, addCount } = useCount();
const add = () => {
  addCount();
};
</script>

<template>
  <div class="cpn-b">
    <div>组件B</div>
    <div>Count:{{ count }}</div>
    <button @click="add">click</button>
  </div>
</template>

<style scoped>
.cpn-b {
  height: 300px;
  background-color: orange;
}
</style>
