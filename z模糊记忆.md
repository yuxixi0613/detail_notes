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

