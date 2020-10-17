# fed-e-task-03-05

### 1、Vue 3.0 性能提升主要是通过哪几方面体现的？
（1）响应式系统升级，3.0 中使用 Proxy 对象重写响应式系统 （2）编译优化，3.0 中标记和提升所有的静态根节点，diff 的时候只需要对比动态节点内容 （3）源码体积的优化，3.0 中移除了一些不常用的 API，例如 inline-template、filter 等，可以让代码最终的体积变小，移除的 filter 可通过 methods, computed来实现

### 2、Vue 3.0 所采用的 Composition Api 与 Vue 2.x使用的Options Api 有什么区别？
在vue2中，我们会在一个vue文件中methods，computed，watch，data中等等定义属性和方法，共同处理页面逻辑，一个功能往往需要在不同的vue配置项中定义属性和方法，比较分散，项目小还好，清晰明了，但是项目大了后，一个methods中可能包含20多个方法，你往往分不清哪个方法对应着哪个功能；在vue3 Composition API 中，我们的代码是根据逻辑功能来组织的，一个功能所定义的所有api会放在一起（更加的高内聚，低耦合），这样做，即时项目很大，功能很多，我们都能快速的定位到这个功能所用到的所有API，而不像vue2 Options API 中一个功能所用到的API都是分散的，需要改动功能，到处找API的过程是很费劲的

### 3、Proxy 相对于 Object.defineProperty 有哪些优点？
（1）Proxy 可以直接监听对象而非属性；
（2）Proxy 可以直接监听数组的变化；
（3）Proxy 有多达 13 种拦截方法,不限于 apply、ownKeys、deleteProperty、has 等等是 Object.defineProperty 不具备的；
（4）Proxy 返回的是一个新对象,我们可以只操作新的对象达到目的,而 Object.defineProperty 只能遍历对象属性直接修改；

### 4、Vue 3.0 在编译方面有哪些优化？
（1）Fragments （升级 vetur 插件）片段的新特性，模板中不需要再创建一个唯一的根节点，模板中可以直接入文本内容或很多同级的标签，在 vscode 中需 升级 vetur 插件
（2）静态提升，通过标记和提升静态节点，通过 Patch flag 将来在 diff 的时候跳过静态根节点，只需要去更新动态节点中的内容，提升了diff的性能
（3）缓存事件处理函数，减少了不必要的操作

### 5、Vue.js 3.0 响应式系统的实现原理？
Vue3 使用 Proxy 对象重写响应式系统，这个系统主要有以下几个函数来组合完成的：
1、reactive:
接收一个参数，判断这参数是否是对象。不是对象则直接返回这个参数，不做响应式处理
创建拦截器对象 handler, 设置 get/set/deleteProperty
get
收集依赖（track）
返回当前 key 的值。
如果当前 key 的值是对象，则为当前 key 的对象创建拦截器 handler, 设置 get/set/deleteProperty
如果当前的 key 的值不是对象，则返回当前 key 的值
set
设置的新值和老值不相等时，更新为新值，并触发更新（trigger）
deleteProperty
当前对象有这个 key 的时候，删除这个 key 并触发更新（trigger）
返回 Proxy 对象
2、effect: 接收一个函数作为参数。作用是：访问响应式对象属性时去收集依赖
3、track:
接收两个参数：target 和 key
如果没有 activeEffect，则说明没有创建 effect 依赖
如果有 activeEffect，则去判断 WeakMap 集合中是否有 target 属性，
WeakMap 集合中没有 target 属性，则 set(target, (depsMap = new Map()))
WeakMap 集合中有 target 属性，则判断 target 属性的 map 值的 depsMap 中是否有 key 属性
depsMap 中没有 key 属性，则 set(key, (dep = new Set()))
depsMap 中有 key 属性，则添加这个 activeEffect
4、trigger:
判断 WeakMap 中是否有 target 属性
WeakMap 中没有 target 属性，则没有 target 相应的依赖
WeakMap 中有 target 属性，则判断 target 属性的 map 值中是否有 key 属性，有的话循环触发收集的 effect()