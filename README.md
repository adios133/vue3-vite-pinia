## pinia

一个全新的用于 Vue 的状态管理库
下一个版本的 Vuex，也就是 Vuex 5.0
Pinia 已经被纳入官方账户下了

相比 vuex 的优势

- 完整的 TypeScript 支持：与在 Vuex 中添加 TypeScript 相比，添加 TypeScript 更容易
- 极其轻巧(体积约 1KB)
- store 的 action 被调度为常规的函数调用，而不是使用 dispatch 方法或 MapAction 辅助函数，这在 Vuex 中很常见
- 支持多个 Store
- 支持 Vue devtools、SSR 和 webpack 代码拆分

Pinia API与Vuex≤4有很大不同，即:

- 没有`mutations` 。mutations被认为是非常冗长的。最初带来了devtools集成，但这不再是问题。
- 不再有模块的嵌套结构。您仍然可以通过在另一个store 中导入和使用store来隐式嵌套store，但Pinia通过设计提供扁平结构，同时仍然支持store之间的交叉组合方式。您甚至可以拥有store的循环依赖关系。
- 更好 typescript支持。无需创建自定义的复杂包装器来支持TypeScript，所有内容都是类型化的，并且API的设计方式尽可能地利用TS类型推断。
- 不再需要注入、导入函数、调用它们，享受自动补全!
- 无需动态添加stores，默认情况下它们都是动态的，您甚至不会注意到。请注意，您仍然可以随时手动使用store来注册它，但因为它是自动的，所以您无需担心。
- 没有命名空间模块。鉴于store 的扁平架构，“命名空间" store是其定义方式所固有的，您可以说所有stores都是命名空间的。

Pinia就是更好的Vuex，建议在你的项目中可以直接使用它了，尤其是使用了TypeScript的项目。



### 使用

挂载

```js
import { createApp } from 'vue';
import './style.css';
import App from './App.vue';
// 引入createPinia
import { createPinia } from 'pinia';

createApp(App).use(createPinia()).mount('#app');
```

定义store

```js
import { defineStore } from 'pinia';

// 对象形式定义
export const useList = defineStore('counts', {
  state: () => {
    return {
      counts:5
    };
  },
  actions: {
    addCount() {
      this.counts++
    }
  },
  getters: {
    listFiltered(state) {
      return state.list.slice(0, 2);
    },
  },
});

// 函数形式定义
// ref
export const useCount = defineStore('count', () => {
  const count = ref(0);
  function addCount() {
    count.value++;
  }
  return {
    count,
    addCount,
  };
});

```



组件中使用

```vue
<script setup>
import { useCount } from '../store';
import {storeToRefs} from 'pinia'
import { useList } from '../store';
const list = useList();
    
// 直接结构会丢失响应式
// const { count, addCount } = useCount()

const counter = useCount()
// count 响应式
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



```

修改state

```js
import { useList } from '../store';
const list = useList();
const add = () => {
  // 1.直接改state
  list.counts++
  // 2.调用action
  // list.addCount(params)
  // 3.使用$patch  obj func   批量更新
  // list.$patch({ counts: list.counts + 1 })
  // list.$patch(state => {
  //  	state.XXX = XXX
  // })
};
```

重置state

```js
const store = useStore()

store.$reset()
```



#### getters

```js
import { defineStore } from 'pinia'
import { otherState } from "@/store/otherState.js";

export const useMainStore = defineStore('main'， {
  state: () => {
    return {
      count: 100,
      foo: "bar",
      arr: [1,2,3]
    }
  },
  // 类似组件的 computed, 用来封装计算属性，有缓存的功能
  gettters: {
      // 函数接受一个可选参数 state 状态对象
      countPlus10(state) {
          console.log('countPlus调用了')
          return state.count + 10
      }
      // 如果getters 中使用了this不接受state参数，则必须手动指定返回值的类型，否则无法推导出来
       countPlus20(): number{
          return this.count + 10
      }
      
       // 获取其它 Getter， 直接通过 this
      countOtherPlus() {
          return this.countPlus20;
      }

      // 使用其它 Store
      otherStoreCount(state) {
          // 这里是其他的 Store，调用获取 Store，就和在 setup 中一样
          const otherStore = useOtherStore();
          return otherStore.count;
      },
      
  }
})

```



#### 异步action

action 支持 async/await 的语法，轻松应付异步处理的场景。

```js
export const useUserStore = defineStore('user', {
    actions: {
        async login(account, pwd) {
            const { data } = await api.login(account, pwd)
            return data
        }
    }
})
```

action 间的相互调用，直接用 this 访问即可。

```js
export const useUserStore = defineStore('user', {
  actions: {
    async login(account, pwd) {
      const { data } = await api.login(account, pwd)
      // 调用sendData
      this.sendData(data) // 调用另一个 action 的方法
      return data
    },
    
    sendData(data) {
      console.log(data)
    }
  }
})
```

在 action 里调用其他 store 里的 action 也比较简单，引入对应的 store 后即可访问其内部的方法了。

```js
// src/store/user.ts
import { useAppStore } from './app'
export const useUserStore = defineStore('user', {
    actions: {
        async login(account, pwd) {
            const { data } = await api.login(account, pwd)
            const appStore = useAppStore()
            appStore.setData(data) // 调用 app store 里的 action 方法
            return data
        }
    }
})
```





https://juejin.cn/post/7057443820115329055

https://pinia.vuejs.org

https://pinia.vuejs.org/core-concepts/state.html#usage-with-the-options-api
