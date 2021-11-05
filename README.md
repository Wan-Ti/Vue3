# Vue3-Notes

## Vue2和Vue3的区别

90%写法完全一致除了以下几点：
Vue3的Template支持多个跟标签，Vue2不支持；</br>
Vue3有createApp(),而Vue 2 的是new Vue();</br>
createApp(组件)，new Vue({template,render})

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






















































































