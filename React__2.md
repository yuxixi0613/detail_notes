[TOC]

# Hook







## 基础Hook

### 1.useState---状态钩子

```javascript
const [state, setState] = useState(initialState);
```

返回一个 state，以及更新 state 的函数。

在初始渲染期间，返回的状态 (`state`) 与传入的第一个参数 (`initialState`) **（初始值）**值相同。

`setState` 函数用于更新 state。它接收一个新的 state 值并将组件的一次重新渲染加入队列。

```javascript
setState(newState);
```

在后续的重新渲染中，`useState` 返回的第一个值将始终是更新后最新的 state。



------



### 2.useEffect---副作用钩子

```javascript
useEffect(()  =>  {
  // Async Action
}, [dependencies])
```



#### 两个参数

> - 第一个参数是一个函数，异步操作的代码放在里面。
> - 第二个参数是一个数组，用于给出 Effect 的依赖项，只要这个数组发生变化，`useEffect()`就会执行。
>
> - 第二个参数可以省略，这时每次组件渲染时，就会执行useEffect



#### 常见用途

> 只要是副效应，都可以使用`useEffect()`引入。它的常见用途有下面几种。
>
> - 获取数据（data fetching）
> - 事件监听或订阅（setting up a subscription）
> - 改变 DOM（changing the DOM）
> - 输出日志（logging）



#### 清除 effect

> `useEffect()`允许返回一个函数，在组件卸载时，执行该函数，清理副效应。



#### 注

> 使用`useEffect()`时，有一点需要注意。如果有多个副效应，应该调用多个`useEffect()`，而不应该合并写在一起



------

### 3.useContext---共享状态钩子

> 如果需要在组件之间共享状态，可以使用`useContext()`。



- React Context API，在组件外部建立一个 Context

```javascript
const AppContext = React.createContext({});
```

- `AppContext.Provider`提供了一个 Context 对象，这个对象可以被子组件共享

  ```react
  <AppContext.Provider value={{
    username: 'laowang'
  }}>
    <div className="App">
      <Navbar/>
    </div>
  </AppContext.Provider>Copy to clipboardErrorCopied
  ```

- `useContext()`钩子函数用来引入 AppContext对象，从中获取`username`属性

  ```javascript
  const { username } = useContext(AppContext)
  ```



------

qs包，查询字符串参数通过qs改为对象

路由

