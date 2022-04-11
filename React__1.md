

[TOC]

# 1.React的基本使用

## 1.1 创建虚拟DOM的两种方式

### 1.1.1 什么是JSX

- react定义的一种类似于XML的JS扩展语法JS + XML，并拥有 JavaScript 的全部功能，本质是React.createElement(component,props, ...children)方法的语法糖
- JSX 生产 React "元素"，你可以将任何的 JavaScript 表达式封装在花括号里，然后将其嵌入到 JSX 中。在编译完成之后，JSX 表达式就变成了常规的 JavaScript 对象，这意味着你可以在 if 语句和 for 循环内部使用 JSX，将它赋值给变量，接受它作为参数，并从函数中返回它，利用JSX实现虚拟DOM。

### 1.1.2 JSX方式

- 全称: JavaScript XML
- react定义的一种类似于XML的JS扩展语法JS + XML本质是React.createElement(component,props, ...children)方法的语法糖
- 作用: 用来简化创建虚拟DOM
- babel.js的作用：
  - 浏览器不能直接解析JSX代码, 需要babel转译为纯JS的代码才能运行
  - 只要用了JSX，就要加上type="text/babel", 声明需要babel来处理

```html
<!-- 准备好一个容器 -->
<div id="test"></div>

<script type="text/babel">
    //1.创建一个虚拟DOM
    let VDOM = (
        <h1 id="title">
            <span>Hello,React!</span>
           </h1> 
    )
    //2.通过react将VDOM转为真实DOM，插入页面
    ReactDOM.render(VDOM,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

### 1.1.3 纯JS方式(开发中不用)

React提供了一些API来创建一种 “特别” 的Object对象

- `const VDOM=React.createElement('xx',{id:'xx'},'xx')`
- 上面创建的就是一个简单的虚拟DOM对象。
- 虚拟DOM对象要会被React转换为真实DOM才能呈现在页面。
- 编码时只需操作虚拟DOM，React能将其转为真实DOM的变化，从而更新界面。

```html
<!-- 准备一个容器 -->
<div id="test"></div>

<script>
    //1.创建一个虚拟DOM(createElement(标签名，标签属性，标签体))
    const VDOM = React.createElement('h1',{id:'title'},React.createElement('span',null,'Hello,React!'))
    //2.通过react将VDOM转为真实DOM，插入页面
    ReactDOM.render(VDOM,document.getElementById('test'))
</script>
```









## 1.2React JSX语法

- 定义虚拟DOM时，不要写引号。
- 标签结构中要混入js表达式，要用{}做分割，即：{js表达式}
- 指定样式的类名不要用class，必须用className
- 行内样式，要用style={{}}的形式去编写，且像font-size这种属性，要转为fontSize
- 只能有一个根标签
- 标签必须闭合
- 标签首字母:
  - 若标签首字母小写：则将该标签转为html中同名元素，若html中无该元素，则报错。
  - 若标签首字母大写：则react去渲染对应的组件，若没有定义过该组件，则报错。





------



# 2. 组件基本理解和使用

## 2.1 用函数定义组件

### 2.1.1 函数组件定义方式

- 函数定义的组件被称作为函数式组件（无状态组件，简单组件）
- 组件名首字母要求大写
- 函数式组件内部需要返回一个虚拟DOM

### 2.1.2 函数式组件特点

- 会被更新并挂载，但是没有生命周期函数
- 没有this(默认被babel编译为严格模式）
- 没有内部状态（state）
- 总结：无状态，也就是你无法使用State，也无法使用组件的生命周期方法，这就决定了函数组件都是展示性组件，接收Props，渲染DOM，而不关注其他逻辑。所以很轻量，如果你的组件没有涉及到内部状态，只是用来渲染数据，那么就用函数式组件，性能较好。

### 2.1.3 组件的使用

- 组件的使用：
  - 直接在ReactDOM.render的第一个参数中上，把组件名放在尖括号中使用（比如：`<ConponentName/>`）
  - 注意，原生 HTML 元素名以小写字母开头，而自定义的 React 组件以大写字母开头
  - 组件标签必须闭合
- 调用组件后到渲染的过程：
  - React发现了`<Component/>`标签，确认是虚拟DOM还是组件
  - 寻找`<Component/>`组件定义的位置，如果没有找到，则报错 XXX is not defined
  - React发现XXXX组件使用函数定义的，React去调用XXX函数，将返回的虚拟DOM结构渲染到页面

```html
<!-- 准备一个容器 -->
<div id="test"></div>

<script type="text/babel">
    //1.使用函数定义一个组件
    function Hello(){
        console.log(this)
        return <h2>我是用函数定义的组件</h2>
    }

    //2.渲染组件到页面
    ReactDOM.render(<Hello/>,document.getElementById('test'))

</script>Copy to clipboardErrorCopied
```

## 2.2 用类定义组件

### 2.2.1 类组件定义方式

- 通常情况下，我们会使用ES6的`class`关键字来创建React组件(复杂组件)
- 使用类继承React.Component
- 在类中书写一个render方法，函数中返回一个虚拟DOM，当类组件被实例化的时候会自动调用内部的render函数

### 2.2.2 render方法

- render方法放在类的原型对象上，供实例去使用
- render方法的this指向的是类组件的实例对象（组件实例）

### 2.2.3 类组件的使用

- 和函数式组件使用方法一致
- 调用组件后到渲染的过程：
  - 渲染`<Component/>`组件，寻找组件定义，发现是一个类式组件（如果找不到则报错）
  - React帮我们实例化的这个类，得到一个组件实例c，并通过组件实例c调用render方法
  - 将render方法返回的虚拟DOM渲染到页面中

```html
<!-- 准备一个容器 -->
<div id="test"></div>

<script type="text/babel">
    //使用ES6中的class定义一个组件
    class Hello extends React.Component{
        render(){
            console.log(this)
            return <h2>我是用类定义的组件</h2>
        }
    }
    //渲染组件到页面
    ReactDOM.render(<Hello/>,document.getElementById('test')

</script>Copy to clipboardErrorCopied
```

## 2.3 组合组件

- 复合组件其实就是组件可以在其输出中引用其他组件
- 无论是类组件还是函数组件，都可以书写复合组件



------





# 3. 组件实例三大核心属性1: state

## 3.1 什么是state

1. state是组件对象最重要的属性, 值是对象(可以包含多个key-value的组合)
2. 组件被称为"状态机", 通过更新组件的state来更新对应的页面显示(重新渲染组件)，一句话就是说，用户的界面会随着状态的改变而改变。
3. state 只能在本组件中去初始化，并且 state 只能被本组件去修改和访问，不能被外部访问和修改，所以也可以说，state 是组件私有的。

## 3.2

### 3.2.1 state的定义

- state状态是设置给当前的实例化对象的,直接在constructor中书写`this.state={key:value}`
- 要求：state必须为一个对象
- 在当前的类组件中，可以通过this.state.key获取到对应状态某个属性的值（请注意this的指向是否正确，应该this指向组件实例）

```html
//1.初始化state
<!-- 准备好一个容器 -->
<div id="test"></div>

<script type="text/babel">
    //定义组件
    class Mood extends React.Component{
        constructor(props){
            super(props)
            this.state = {isHot:false,wind:'愤怒'} 
        }
        render(){
            return (
                <div>
                    <h2>今天天气很{this.state.isHot ? '愤怒' : '愉快'}</h2>
                </div>
            )
        }
    }
    //渲染组件
    ReactDOM.render(<Mood/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

### 3.2.2 state简写

1. 在开发中，初始化state一般直接在类中使用： state = {}

2. 如果没有其他需求则可以直接省略constructor的书写

   ```js
   class Weather extends React.Component{
       //初始化状态
       state = {isHot:true}
   }Copy to clipboardErrorCopied
   ```

## 3.3 React中的事件

1. React底层并没有使用原生的DOM事件，而是采用了自定义事件（合成事件）。
2. React实现了一套高效的事件系统，包含：事件注册、事件存储、事件绑定、事件分发等全套逻辑。在原生DOM事件体系基础上有了很大的改进：减少了内存的消耗、且最大程度上解决了浏览器兼容性问题。
3. React中的事件都是通过事件委托的方式实现的。
4. React中通过onXxxx去绑定事件（注意大小写）
5. 可以通过event.target得到发生事件的DOM元素。
6. 在 React 中不能通过返回 `false` 的方式阻止默认行为。必须显式的使用 `preventDefault`

### 3.3.1 react绑定事件

- 在任意位置定义一个函数，作为事件函数
- 在应该被绑定事件的虚拟DOM的属性上添加事件属性，格式是onXxxxx(例如onClick)，注意这个事件属性是react封装的，而不是原生js中的语法
- 虚拟DOM的事件属性的值是事件函数的名称，并且要放在{}中，当事件发生的时候，改函数会立即调用

```html
<!-- 准备好一个容器 -->
<div id="test"></div>

<script type="text/babel">
    //定义组件
    class Mood extends React.Component{
        constructor(props){
            super(props)
            this.state = {isHot:false,wind:'愤怒'} 
        }
        render(){
            return (
                <div>
                    <h2 onClick={demo}>今天天气很{this.state.isHot ? '愤怒' : '愉快'}</h2>
                </div>
            )
        }
    }
    function demo(){
        alert("hello")
    }
    //渲染组件
    ReactDOM.render(<Mood/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

### 3.3.2react绑定事件优化

- 事件函数直接通过`xxxx(){}`的方式写在类中，作为类的原型对象的方法，这样可以才可能再事件函数中获取state状态，并且符合逻辑

- 事件函数是当事件发生的时候被调用，但是不是被组件实例调用的，属于默认调用，所以这种方式事件函数的this指向undefined(默认严格模式)，无法使用this.state得到state状态对象

- 我们希望这个事件函数的this是指向组件实例的，这样才可以通过

  ```
  this.state
  ```

  得到状态对象

  - 类的构造函数中this指向组件实例，所以可以先在构造器中使用bind调用事件函数，并修改函数的this指向为组件实例
  - 在构造器中给组件实例设置一个新的对象的key，然后把bind返回的函数赋值给这个key，这个key作为新的事件函数

```html
<script type="text/babel">
    //定义组件
    class Mood extends React.Component{
        constructor(props){
            super(props)
            //下面这行是为了解决changeWea中this指向的问题
            this.gaiXinQing = this.changeMood.bind(this)
        }
        render(){
            return (
                <div>
                    <h2 onClick={this.gaiXinQing}>今天心情很{this.state.isHot ? '愤怒' : '愉快'}</h2>
    </div>
            )
        }
                
        //类式组件中的方法如果做事件回调去使用，this是undefined
        changeMood(){ 
            console.log(this)
        }
    }
    //渲染组件
    ReactDOM.render(<Mood/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

### 3.3.3 react绑定事件优化2]

将事件函数在类中写成箭头函数，箭头函数没有自己的this，当事件函数被调用的时候，this使用的是当前环境的this（指向的是组件实例）

所以在开发中，把类中所有事件回调的函数，都写成箭头函数，且直接放在组件实例自身。

```html
<script type="text/babel">
    //定义组件
    class Mood extends React.Component{
        render(){
            return (
                <div>
                    <h2 onClick={this.changeMood}>今天心情很{this.state.isHot ? '愤怒' : '愉快'}</h2>
    </div>
            )
        }
                
        //类式组件中的方法如果做事件回调去使用，this是undefined
        changeMood = () => { 
            console.log(this)
        }
    }
    //渲染组件
    ReactDOM.render(<Mood/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

### 3.3.4 修改state状态对象

- 不可以直接通过this.state.xxx=xxx的方式修改state对象的内容
- 修改state一定要调用setState这个方法去修改
  - 使用语法：setState({key:value})
- setState做了两件事：1. 改变state 2. 重新调用了一次render

```html
<script type="text/babel">
    //定义组件
    class Mood extends React.Component{
        render(){
            return (
                <div>
                    <h2 onClick={this.changeMood}>今天心情很{this.state.isHot ? '愤怒' : '愉快'}</h2>
    </div>
            )
        }
                
        //类式组件中的方法如果做事件回调去使用，this是undefined
        changeMood = () => { 
            console.log(this)
            //state中的数据不可以直接修改，下面这行就是直接修改
            // this.state.isHot = true

            //获取原状态
            const {isHot} = this.state
            this.setState({isHot:!isHot}) 
        }
    }
    //渲染组件
    ReactDOM.render(<Mood/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

# 4. 组件实例三大核心属性2:props

## 4.1 理解

1. **props就是组件定义属性的集合,**它是组件对外的接口,由外部通过JSX属性传入设置(也就是从外部传递给内部组件的数据)
2. 每个组件对象都会有props(properties的简写)属性
3. 组件标签的所有属性都保存在props中

## 4.2 作用

1. 通过标签属性从组件外向组件内传递变化的数据,**外部世界(组件)就是通过prop来和组件进行对话数据传递的**
2. 注意: 组件内部不要修改props数据

## 4.3 案例

需求: 自定义用来显示一个人员信息的组件

1. 姓名必须指定，且为字符串类型；
2. 性别为字符串类型，如果性别没有指定，默认为男
3. 年龄为字符串类型，且为数字类型，默认值为18

![5Wo8aV.jpg](https://z3.ax1x.com/2021/10/24/5Wo8aV.jpg)

### 4.3.1 props的基础使用

直接在render中通过`this.props`获取用户调用组件的时候传入的数据，插入到虚拟DOM中

```html
<!-- 准备好一个容器 -->
<div id="test"></div>
<div id="test2"></div>

<script type="text/babel">
    //定义Person组件
    class Person extends React.Component{
        render(){
            console.log(this)
            const {name,age,sex} = this.props
            //思考：如果想要在每一个名字后添加一个感叹号该如何去做？
            // this.props.name = this.props.name + '!' //该代码会引起报错，因为props是只读的
            //name+=! //可以使用
            return (
                <ul>
                    <li>姓名：{name}</li>    
                    <li>性别：{sex}</li>    
                    <li>年龄：{age}</li>    
                </ul>
            )
        }
    }
    //渲染组件
    ReactDOM.render(<Person name="老刘" age="18" sex="男"/>,document.getElementById('test'))
    ReactDOM.render(<Person name="老王" age="19" sex="女"/>,document.getElementById('test2'))
</script>Copy to clipboardErrorCopied
```

### 4.3.2 props值的修改

- props对象中的属性都是只读的，不能直接修改，比如`this.props.name="xxx"`是错误的
- 可以先定义个变量得到到某个属性值：`const {name}=this.props`，然后再修改这个变量是允许的

```js
const {name,age,sex} = this.props;
//思考：如果想要在每一个名字后添加一个感叹号该如何去做？
// this.props.name = this.props.name + '!' //该代码会引起报错，因为props是只读的
name+=! //可以使用Copy to clipboardErrorCopied
```

### 4.3.3 批量传递props

- 当需要把一个对象内所有的key-value都传递到组件中，可以使用批量传递
- 比如一个对象obj，我们可以在传递props的时候直接书写为`{...obj}`即可
- 当然，我们可以传递额外的props来添加和修改批量传递的内容

```html
<script type="text/babel">
    //定义Person组件
    class Person extends React.Component{
        render(){
            console.log(this)
            const {name,age,sex,address,car} = this.props
            return (
                <ul>
                    <li>姓名：{name}</li>    
                    <li>性别：{sex}</li>    
                    <li>年龄：{age}</li>    
                    <li>住址：{address}</li>    
                    <li>座驾：{car}</li>    
                </ul>
            )
        }
    }

    let p1 = {
        name:'laowang',
        age:18,
        sex:'女',
        address:'中铁七局',
        car:'奔驰c63'
    }


    //渲染组件
    // ReactDOM.render(<Person name={p1.name} age={p1.age} sex={p1.sex} address={p1.address} car={p1.car}/>,document.getElementById('test'))

    //批量传递props
    //正常情况下...obj是会报错的，但是react+babel就可以完成...obj，且仅仅适用于标签属性的传递
    ReactDOM.render(<Person {...p1}/>,document.getElementById('test'))Copy to clipboardErrorCopied
```

### 4.3.4 配置props限制

- 引入react的propType.js第三方包
- prop-types的主要作用：对props中数据类型进行检测及限制
- 使用：
  - 给组件扩展静态属性propTypes，值是一个对象
  - key就是被限制的props属性
  - value是的写法：
    - 可以限制值的数据类型（number、string、bool、func、object、array等）`PropTypes.XXXX`
    - 限制特定的内容：`PropTypes.oneOf(['a', 'b'])`,值只能是a或者b
    - 在一定范围内的类型:PropTypes.oneOftype([PropTypes.string,PropTypes.number])
    - 也可以在任何 PropTypes 属性后面加上 `isRequired` 后缀，这样如果这个属性父组件没有提供时，会打印警告信息
  - 可以给props配置默认值
    - 直接给 组件扩展静态属性defaultProps，是一个对象，对象内书写默认props属性和值
  - 更多可以查看文档 https://www.npmjs.com/package/prop-types
- 像 [Flow](https://flow.org/) 和 [TypeScript](https://www.typescriptlang.org/) 等这些静态类型检查器，可以在运行前识别某些类型的问题。他们还可以通过增加自动补全等功能来改善开发者的工作流程。出于这个原因，我们建议在大型代码库中使用 Flow 或 TypeScript 来代替 `PropTypes`。

```html
<script type="text/babel">
    //定义Person组件
    class Person extends React.Component{
        //限制props的类型以及必要性
        static propTypes = {
            name:PropTypes.string.isRequired, //name必须传且为字符串
            age:PropTypes.number.isRequired,//age必须传且为数值
            sex:PropTypes.string.isRequired,//sex必须传且为字符串
            address:PropTypes.string //address可传可不传，但是传必须是字符串
        }

        //给props指定默认值，注意：可传可不传的属性，才需要配置默认值
        static defaultProps = {
            address:'火星'
        }

        render(){
            const {name,age,sex,address} = this.props
            return (
                <ul>
                    <li>姓名：{name}</li>    
                    <li>性别：{sex}</li>    
                    <li>年龄：{age+1}</li>    
                    <li>住址：{address}</li>    
                </ul>
            )
        }
    }

    let p1 = {
        name:'老刘',
        age:18,
        sex:'女',
        address:'我心里'
    }
    ReactDOM.render(<Person {...p1}/>,document.getElementById('test'))

    ReactDOM.render(<Person name="老王" age={19} sex="男"/>,document.getElementById('test2'))
</script>Copy to clipboardErrorCopied
```

## 4.4 函数式组件使用props

函数组件中的props是一个对象直接作为函数的参数传入,在组件内容中可以直接用点语法使用props对象中的属性,代码如下:

```html
<script type="text/babel">

    function Person(props){
        const {name,sex,age,address} = props
        return (
            <ul>
                <li>姓名：{name}</li>    
                <li>性别：{sex}</li>    
                <li>年龄：{age}</li>    
                <li>住址：{address}</li>    
            </ul>
        )
    }

    //限制props的类型以及必要性
    Person.propTypes = {
        name:PropTypes.string.isRequired, //name必须传且为字符串
        age:PropTypes.number.isRequired,//age必须传且为数值
        sex:PropTypes.string.isRequired,//sex必须传且为字符串
        address:PropTypes.string //address可传可不传，但是传必须是字符串
    }

    //给props指定默认值，注意：可传可不传的属性，才需要配置默认值
    Person.defaultProps = {
        address:'火星'
    }

    let p1 = {
        name:'老刘',
        age:18,
        sex:'女',
        address:'我心里'
    }
    ReactDOM.render(<Person {...p1}/>,document.getElementById('test'))

</script>Copy to clipboardErrorCopied
```

# 5. 组件实例三大核心属性3:refs

Refs 提供了一种方式，允许我们访问 DOM 节点或在 render 方法中创建的 React 元素。

## 5.1 效果

需求: 自定义组件, 功能说明如下:

1. 效果1：点击按钮, 提示第一个输入框中的值
2. 效果2：当第2个输入框失去焦点时, 立马提示这第二个输入框中的值

![5fwldI.png](https://z3.ax1x.com/2021/10/24/5fwldI.png)

## 5.2 过时 API：String 类型的 Refs

- 可以给我们要获取的元素上设置string 类型的 ref 属性，例如`ref='oInput'`，我们可以通过this.refs.oInput来访问这个DOM节点
- 我们不建议使用它，因为 string 类型的 refs 存在 [一些问题](https://github.com/facebook/react/pull/8333#issuecomment-271648615)。它已过时并可能会在未来的版本被移除。

```html
<script type="text/babel">
    //定义组件
    class Demo extends React.Component{
        render(){
            return (
                <div>    
                    <input ref="userinput" type="text"/>&nbsp;
                    <button onClick={this.showData}>点我提示左侧数据</button>
                </div>
            )
        }
        showData = ()=>{
            // console.log(this)
            alert(this.refs.userinput.value)
            }
    }
    //渲染组件
    ReactDOM.render(<Demo/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

## 5.3 回调函数形式的ref

- React 也支持另一种设置 refs 的方式，称为“回调 refs”。它能助你更精细地控制何时 refs 被设置和解除。
- 使用：
  - ref属性值是一个函数，请把函数放在{}中，也就是说在ref属性中书写了一个回调函数
  - 当渲染到ref属性的时候，react会先让这个回调函数默认调用
  - 这个ref的回调函数希望写成箭头函数的形式，这样函数内this就能指向组件实例了
  - 当这个回调函数被调用的时候，react会向回调函数中传递一个实参，实参是当前ref所在的DOM节点,所以这个回调函数可以接受一个形参，形参就是当前的DOM元素
  - 在回调函数中，把这个DOM元素赋值给组件实例上的一个属性，即可以在其他位置通过组件实例的这个属性获得这个DOM元素

```html
<script type="text/babel">
    //定义组件
    class Demo extends React.Component{
        render(){
            return (
                <div>    
                    <input ref={ c => this.userinput = c } type="text"/>&nbsp;
                    <button onClick={this.showData}>点我提示左侧数据</button>&nbsp;
                    </div>
            )
        }
        showData = ()=>{
            alert(this.userinput.value)
        }
    }
    //渲染组件
    ReactDOM.render(<Demo/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

## 5.4 createRef的使用

- Refs 是使用 `React.createRef()` 创建的，并通过 `ref` 属性附加到 React 元素。在构造组件时，通常将 Refs 分配给实例属性，以便可以在整个组件中引用它们。
- 对该节点的引用可以在 ref 的 `current` 属性中被访问

```html
<script type="text/babel">
    //定义组件
    class Demo extends React.Component{
        //创建一个承装被ref所标记节点的容器——该容器是专人专用的（只能存一个）
        userInput = React.createRef()
        btn = React.createRef()
            render(){
            return (
                <div>    
                    <input ref={this.userInput} type="text"/>&nbsp;
                    <button ref={this.btn} onClick={this.showData}>点我提示左侧数据</button>&nbsp;
                </div>
            )
        }
        showData = ()=>{
             // const {current:{value:x}} = this.userInput //解构赋值+重命名
            alert(this.userInput.current.value)
        }
    }
    //渲染组件
    ReactDOM.render(<Demo/>,document.getElementById('test'))
</script>Copy to clipboardErrorCopied
```

## 5.5 事件对象event

- React中绑定的事件函数也可以接受一个参数是event事件对象
- 当操作的DOM是自身事件发生的元素的时候，可以不使用ref，直接通过e.target获取即可

```html
<input type="text" onBlur={this.showMsg}  placeholder="失去焦点的时候弹出输入内容" />
<script>
    showMsg = (event)=>{
        console.log(event);
        //event事件对象有一个属性是target，代表事件发生的目标元素
        console.log(event.target);
        alert(event.target.value);
    }
</script>Copy to clipboardErrorCopied
```





------





# [6. 类式组件中构造器的说明](http://docs.lipeihua.vip/#/./react/02.React组件化开发?id=_6-类式组件中构造器的说明)

1. 一般在开发中我们从来不写构造器，因为原本需要在构造器中完成的如下两个任务：

 (1).通过给 this.state 赋值对象来初始化内部 state

 (2).解决事件处理函数中this的问题

 均可以通过如下两个方式搞定：

 (1).state = {}完成初始化state

 (2).changeWeather = ()=>{} 解决this指向问题

1. 如果写了构造器，那么构造器中必调用super，调用super时传不传入props呢？

 (1).如果传递，在super语句之后，可以通过this.props正常获取props

 (2).如果不传递，在构造器中this.props是undefined。（此场景几乎开发中不出现）

```html
<script type="text/babel">
    //定义组件
    class Weather extends React.Component{
        constructor(props){
            // console.log('constructor',props)
            super(props)
            console.log('%%%',this.props)
            this.state = {isHot:true}
        }

        render(){
            return (
                <div>
                    <h2>今天天气很{this.state.isHot ? '炎热' : '凉爽'}</h2>
                    <h4>我收到了一个人的信息：{this.props.name}--{this.props.age}</h4>
                    <button onClick={this.changeWeather}>切换天气</button>
                </div>
            )
        }

        changeWeather = ()=>{
            //获取原来的isHot值
            const {isHot} = this.state
            //更新状态
            this.setState({isHot:!isHot})
        }
    }

    //渲染组件
    ReactDOM.render(<Weather name="老刘" age={18}/>,document.getElementById('test'))
</script>
```





------













