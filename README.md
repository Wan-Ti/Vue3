# Vue3-Notes

## Vue2和Vue3的区别

90%写法完全一致除了以下几点：
Vue3的Template支持多个跟标签，Vue2不支持；</br>
Vue3有createApp(),而Vue 2 的是new Vue();</br>
createApp(组件)，new Vue({template,render})；</br>
使用ref创建内部数据；</br>

### v-model

使用新的v-demo来代替以前的v-demol和.sync;</br>
新增context.emit,与this.$emit作用相同

### Vue3属性绑定

默认所有属性都绑定到根元素；</br>
使用inheritAttrs:false可以取消默认绑定；</br>
使用$attrs或者context.attrs获取所有属性；</br>
使用```v-bind="$attrs"```批量绑定属性；</br>
使用 ```const{a,...b} = context.attrs将属性分开```</br>
a:属性a;b:其余属性

### 自定义组件
自定义组件的过程中如何在运行时确认子组件的类型：检查``` context.slots.default() ```数组

### JS小技巧

#### 使用JS获取插槽内容
``` const defaults = context.slots.default() ```

#### 获取宽高位置

``` const {width,height,top,left} = e.getBoundingClientRect() ```

#### ES6析构赋值的重命名语法

``` 
const {left:left1} = x.getBoundingClientRect()
const {left:left2} = y.getBoundingClientRect()
```

### 钩子

onMounted,onUpdated,watchEffect.其中watchEffect发生在onMounted之前

### TypeScript

const indeicator = ref<HTMLDivElement>(null)


## vue-router4
vue-router4专为支持vue3而创造

**出现问题vue 3 con not find modules**

当main.js改为main.ts文件后，编辑器会报错： ``` can not find modules 'xxx.vue' ```。</br>
这是因为ts只能识别.ts文件，不能识别.vue文件。
当我们使用chrome进行英文搜索：'vue 3 con not find modules'.可以看到这个问题的原因以及解决办法：
![image](https://user-images.githubusercontent.com/47940363/140441220-cbe60591-bf90-4ae7-a214-0f4a4a577367.png)
```
所以我们需要在src目录下新建一个后缀名为.d.ts文件。告诉ts如何去识别.vue文件。</br>
文件内容：</br>
declare module '*.vue' {
  import { DefineComponent } from 'vue'
  // eslint-disable-next-line @typescript-eslint/no-explicit-any, @typescript-eslint/ban-types
  const component: DefineComponent<{}, {}, any>
  export default component
}
```
这样就可以了
## 项目搭建
使用Vue3+ts+router4+vuex搭建项目：</br>
1：快速搭建项目：</br>
```
npm init vite vue3-demo //或 yarn create vite vue3-demo
```
记得选择vue-ts这样就会自动帮我们写好shime-vue.d.ts：</br>
![image](https://user-images.githubusercontent.com/47940363/140440400-3ead8c98-6206-4015-a1b2-02da2dd58855.png)</br>
![image](https://user-images.githubusercontent.com/47940363/140440466-4a2a1c06-7213-4757-85d3-aa9137c67d76.png)</br>
![image](https://user-images.githubusercontent.com/47940363/140440488-1d637504-8108-498c-ba1a-a79fd4756b47.png)</br>
这样我们就成功搭建好了vue3+ts的项目框架。</br>

## vue-router4

1:vue-router不再是一个类，而是一组函数。即不需要再写new Router(),而是调用createRouter;</br>
```
//旧版本
import Router from 'vue-router'

//vue-router4
import { createRouter } from 'vue-router
```
2: 新的history配置取代mode
mode:'history'配置被更灵活的history所取代。根据使用的模式，应当使用适当的函数替换它。

* "history":createWebHistory()
* "hash":createWebHashHistory()
* "abstract":createMemoryHistory()

完整的代码段：
```
import {createWebHistory,createRouter} from 'vue-router'

createRouter({
  history:createWebHistory(),
  routes:[],
})
```

## 父子组件通信：

通过props接收事件；

通过context发出数据；

```
//父组件

<template>
    <div>
        <Switch :value="y"  @input="y = $event" />
    </div>
</template>
<script lang="ts">
    import Switch from "../lib/Switch.vue";
    import { ref } from 'vue'
    export default {
        components:{Switch},
        setup(){
            const y = ref(false)
            return {y}
        }
    }
</script>
```

```
//子组件
<template>
    <button :class="{checked:value}" @click="toggle">
        <span></span>
    </button>
    <div>{{value}}</div>
</template>

<script lang="ts">
    export default  {
        props:{
          value:Boolean
        },
        setup(props: any, context: { emit: (arg0: string, arg1: boolean) => void }){
            const toggle = ()=>{
                context.emit('input',!props.value)
            }
            return {toggle}
        }
    }
</script>

```
子组件通过props接收外部传来的数据-y,此时y的值为false。父组件创建了一个input事件（其实这个事件不一定是input，想设置什么事件都可以）</br>
子组件通过context接收该事件，context有个方法```emit```,这个方法有两个参数，第一个参数为事件名，第二个参数就是父组件传过来的$event.
```
emit(事件名，事件参数)
```


### v-model对父子组件通信进行简化

使用v-model后的代码：
```
//父组件

<template>
    <div>
        <Switch v-model:value="y" />
    </div>
</template>
<script lang="ts">
    import Switch from "../lib/Switch.vue";
    import { ref } from 'vue'
    export default {
        components:{Switch},
        setup(){
            const y = ref(false)
            return {y}
        }
    }
</script>
```

```
//子组件

<template>
    <button :class="{checked:value}" @click="toggle">
        <span></span>
    </button>
</template>

<script lang="ts">
    export default  {
        props:{
          value:Boolean
        },
        setup(props: any, context: { emit: (arg0: string, arg1: boolean) => void }){
            const toggle = ()=>{
                context.emit('update:value',!props.value)
            }
            return {toggle}
        }
    }
</script>
```

注意子组件中，context的第一个参数改为：```'update:x```。</br>
其中x为子组件中props传递过来的参数。


## ref 

接受一个内部值并返回一个响应式且可变的 ref 对象。ref 对象具有指向内部值的单个 property .value。

### toRef 

### toRefs

### isRef










































































