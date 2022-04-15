[TOC]

# vuex

### ...mapstate()

取数据在computed中使用...mapState()

```js
computed: {
    //万能写法：
    ...mapState({
        yyy: (state) => state.xxx
    })	
}
```

> **注：**此处的state是...mapState方法内置的形参，即vuex里的state



------

# 自定义事件与原生事件的$event

#### 自定义事件中的$event

是传递过来的数据

#### 原生事件中的$event

是事件对象

**注意两点:**

> 1. 不使用圆括号，event被自动当作实参传入
> 2. 使用圆括号，必须显式的传入event对象，如果不传入可能最终找到的是全局的window .event



------

# axios传参与路由传参

## axios传参

#### params参数

params参数只能在路径当中去写    /user/1

#### query参数

有两个地方可以写：

1、路径当中以?分割 问号后面的  key=value&key=value

2、在配置项的params里面写query参数

#### data参数（请求体参数）

一般是post请求、put请求携带的比较多，只能写在data配置项中



## 路由传参

#### params参数

#### query参数

------

























