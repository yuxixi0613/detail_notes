[TOC]

# 其他模块化规范



### commonJS

#### 说明

> - 每个文件都可当作一个模块
> - 在服务器端: 模块的加载是运行时同步加载的
> - 在浏览器端: 模块需要提前编译打包处理
> - 同步加载，有缓存

#### 基本语法

> - 暴露模块
>   - exports.xxx = value
>   - module.exports = value
> - 引入模块
>   - require(xxx)

#### 实现

> - 服务器端实现（Node.js）
>
> - 浏览器端实现（Browserify，也称为CommonJS的浏览器端的打包工具）
>
>   - 下载
>
>     npm i browserify -g
>
>   - 编译
>
>     browserify main.js(使用Commonjs规范的代码) -o built.js（可以浏览器解析的js代码）
>
> - 区别Node与Browserify
>
>   - Node.js运行时动态加载模块(同步)
>   - Browserify是在转译(编译)时就会加载打包(合并)require的模块







------





# ES6模块化规范



### 默认暴露及引入

- 暴露：

  - 当前模块只需要暴露一个功能，则可以选用默认暴露
  - 默认暴露在当前模块中只能暴露一个功能
  - 使用 export default XXX;

  ```javascript
  function add(a, b) {
      return a + b;
  }
  
  export default add;Copy to clipboardErrorCopied
  ```

- 引入`import add from './add';`

- `as`可以起别名，比如 `import add as save from './add'`;

### 分别暴露和引入

- 暴露

  - 当一个模块中有多个功能需要暴露的时候，可以在声明语句前进行书写export进行暴露
  - 分别暴露可以暴露多个功能出去
  - 如果是分别暴露 则需要把export写在完整的声明语句前

  ```js
  export let count = 0;
  
  export const msg = {
      code: 10000,
      info: "hello world"
  }
  
  export function mins(a, b) {
      return a - b;
  }Copy to clipboardErrorCopied
  ```

- 引入:

  - 引入分别暴露模块（一般使用解构赋值的形式来引入），因为分别暴露最终暴露的是一个对象
  - 假如真的不想用解构赋值，我们可以直接用一个对象接收住统一暴露的内容`import * as say from './say'`

### 统一暴露

- 暴露

  - 直接在export后放对象，把需要暴露的功能放在对象中暴露出去
  - 一般是暴露多个功能使用

  ```js
  function say1(con) {
      return "hello " + con;
  }
  
  function say2(con) {
      return "bye " + con
  }
  
  //统一暴露
  /* export {
      say1,
      say2
  } */
  
  //统一暴露的时候 起一个别名
  export {
      say1 as s1, //起别名
      say2
  }Copy to clipboardErrorCopied
  ```

- 引入

  - 引入统一暴露模块（一般也是使用解构赋值的形式接受）`import { s1,say2} from './say'`
  - 假如真的不想用解构赋值，我们可以直接用一个对象接收住统一暴露的内容`import * as say from './say'`

### ES6 模块化的编译

使用babel把ES6模块化语法编译为CommonJS模块化

使用browserify把CommonJS模块化编译为浏览器识别的语法

- babel的使用`npm install --save-dev @babel/core @babel/cli @babel/preset-env`
  - @babel/core：babel的核心包
  - @babel/cli：babel的命令包
  - @babel/preset-env：babel的预设包
- 在package.json中配置预设` "babel": {"presets": ["@babel/env"]}`
- 使用babel的命令把js文件夹中所有ES6模块化的文件编译为CommonJS的模块化规范:`npx babel 目标文件夹 -d 新文件夹（npx是启动本地命令）`
- 使用browserify把babel编译出来的CommonJS规范的入口文件代码编译为浏览器识别的代码





