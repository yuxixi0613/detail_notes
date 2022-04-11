[TOC]



## vue指令

### v-bind（强制绑定）

> - 强制绑定，用在属性里，可以让某个属性的值拥有插值功能
> - v-bind  可直接简写为冒号   :

```vue
<input v-bind:value="">
//简写为：
<input :value="">
```

#### class的动态绑定

> - 动态类只有一个的时候 ：class的值是一个字符串
> - 动态类有多个的时候，并且类名个数确定，但是不知道是否使用 ：class的值是一个对象
> - 动态类有多个的时候，不确定使用哪一个 ：class的值是一个数组



#### 动态style

```vue
//以下这个修改是不生效的，因为修改对象 无法驱动视图更新
<button @click="styleEx2[0].background = 'blue'">添加背景颜色</button>

//这样的修改是有效的，因为vue对push等做了特殊处理，可以驱动视图更新
<button @click="styleEx2.push({background:'blue'})">添加背景颜色</button>
```





### v-model（双向绑定）

> 单选就用字符串，多选就用数组。



> **在checkbox复选框**
>
> - 独立的复选框：v-model的数据是一个布尔值来控制选中状态
> - 多个复选框：v-model的数据是一个**数组**，表单中必须书写value，用来收集和一一对应数据



> **在radio单选框**
>
> v-model在单选框中只有一种情况：因为无论是一个还是多个，最终都是选择一个数据，
>
> 保存的是字符串的格式



> **在select下拉选择框**
>
> - 因为select基本都是单选，所以数据是一个**字符串**
> - option标签的value属性和v-model的数据是对应关系
> - option标签内部的文本是展示的文本



#### v-model修饰符

> - .lazy修饰符：
>
>   ​    v-model是视图一旦更新，数据立马跟着更新 类似于input事件
>
>   ​    v-model想要做成change事件的效果（失去焦点才触发更新） 使用.lazy修饰符
>
>   
>
> - .number修饰符
>
>   ​    我们会经常收集表单数据，但是表单数据统一都是字符串类型，如果我们想要让他转为number类型，可以使用number修饰符
>
>   
>
> - .trim修饰符
>
>   ​    去字符串前后空格



### v-on（事件绑定）

```vue
<button v-on:click="handlerClick">点我</button>
// v-on: 简写为 @
<button @click="handlerClick">点我</button>
```



> - 在写事件函数的时候，可以加小括号，也可以不加，唯一区别是是否可以传参
>
>      
>
> - 当给事件函数传递参数的时候，会占用了原来e所在的位置，则我们需要额外传递一个$event关键字，在事件函数中去接受，就是event事件对象



#### 事件修饰符

> **常用的事件修饰符**
>
> 1. prevent:阻止默认事件(常用);
> 2. stop:阻止事件冒泡(常用);
> 3. once:事件只触发一次(常用);
> 4. capture:使用事件的捕获模式;
> 5. self:只要event.target是当前操作的元素时才触发事件;
> 6. passive:事件的默认行为立即执行,无需等待事件回调执行完毕;





## computed计算属性

> **简写**：为一个函数
>
> 只考虑读取时，不考虑修改的时候才能用简写方式



## watch监视属性

immediate

handler

deep

> 
>
> 
>
> 
>
> 
>





pubsub

取消所有订阅，里面需要一个空字符串

unsubscribe("")

> 
>
> 
>
> 
>
> 
