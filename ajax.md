[TOC]



# ajax

### AJAX的四个使用步骤

#### 1.创建XMLHttpRequest对象

`var xhr = new XMLHttpRequest();`

#### 2.设置请求信息

`xhr.open(method, url);`

#### 3.发送请求

##### get请求：

xhr.send():因为请求体在查询字符串中，所以不需要书写参数



##### post请求：

`xhr.send(body)` //get请求不传body参数，只有post请求使用



body如果是查询字符串格式，

则书写请求头`xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');`,

服务器需要使用中间件`app.use(express.urlencoded(extended: true}))`



body如果是json字符串格式，

则书写请求头`xhr.setRequestHeader("content-type", "application/json")`,

服务器需要使用中间件`app.use(express.json())`;



#### 4.接收响应

`onreadystatechange `事件中，当`xhr.readyState == 4 && (xhr.status >= 200 && xhr.status < 300)`时表示成功

`xhr.responseXML` 接收xml格式的响应数据

`xhr.responseText` 接收文本格式的响应数据





##### 注：xhr.readyState:

>  0 :初始化状态
>
>  1 :代表open调用，但send方法还未调用（没有发送请求）
>
>  2 :代表send方法调用，并且接受到了部分响应信息（响应首行和响应头：状态码就在其中）
>
>  3 :代表接受了部分响应体数据，（如果响应体数据较小就全部接受。但是数据如果比较大，就只接受一部分）
>
>  4 :代表全部接受完成



------



### 解决IE缓存问题

问题：在一些浏览器中(IE),由于缓存机制的存在，ajax的get请求只会发送的第一次请求，剩余多次请求不会在发送给浏览器而是直接加载缓存中的数据。chrome/firfox执行协商缓存，IE走强制缓存



解决方式：浏览器的缓存是根据url地址来记录的，所以我们只需要修改url地址即可避免缓存问题

```javascript
xhr.open("get","/testAJAX?t="+Date.now());
```







------



# axios

### axios是什么？

axios是一个函数，由原生的xhr和promise封装出来的，用于简单快捷方便处理的发送异步请求



### axios的函数用法

#### axios可以传几种参数？

params参数  和  query参数  和  data参数（请求体参数）

##### params参数只能在路径当中去写

 		/user/1

##### query参数有了两个地方可以写

1.路径当中以？分割	问好后面的 key=value&key=value

2.在配置项的params里面写query参数

##### data参数：

一般是post请求、put请求携带的比较多，只能写在data配置项中







axios的对象用法

未完待续。。。





### axios特点

> 1. 基于promise的异步ajax请求库
> 2. 浏览器端/node端都可以使用
> 3. 支持请求／响应拦截器
> 4. 支持请求取消
> 5. 请求/响应数据转换
> 6. 批量发送多个请求



### axios常用语法

> axios(config): 通用/最本质的发任意类型请求的方式
>
> axios(url[, config]): 可以只指定url发get请求
>
> axios.get(url[, config]): 发get请求
>
> axios.delete(url[, config]): 发delete请求
>
> axios.post(url[, data, config]): 发post请求
>
> axios.put(url[, data, config]): 发put请求
>
> axios.defaults.xxx: 请求的默认全局配置
>
> axios.interceptors.request.use(): 添加请求拦截器
>
> axios.interceptors.response.use(): 添加响应拦截器
>
> axios.create([config]): 创建一个新的axios(它没有下面的功能)
>
> axios.Cancel(): 用于创建取消请求的错误对象
>
> axios.CancelToken(): 用于创建取消请求的token对象
>
> axios.isCancel(): 是否是一个取消请求的错误
>
> axios.all(promises): 用于批量执行多个异步请求



### 难点语法的理解和使用

#### 1.axios.create(config)

> 1. 根据指定配置创建一个新的axios, 也就就每个新axios都有自己的配置
>
> 2. 新axios只是没有取消请求和批量发请求的方法, 其它所有语法都是一致的
>
> 3. 为什么要设计这个语法?
>
>    (1) 需求: 项目中有部分接口需要的配置与另一部分接口需要的配置不太一样, 如何处理
>
>    (2) 解决: 创建2个新axios, 每个都有自己特有的配置, 分别应用到不同要求的接口请求中



#### 2.拦截器函数/ajax请求/请求的回调函数的调用顺序

> 1. 说明: 调用axios()并不是立即发送ajax请求, 而是需要经历一个较长的流程
> 2. 流程: 请求拦截器2 => 请求拦截器1 => 发ajax请求 => 响应拦截器1 => 响应拦截器2 => 请求的回调
> 3. 注意: 此流程是通过promise串连起来的, 请求拦截器传递的是config, 响应拦截器传递的是response



#### 3.取消请求

> 1. 基本流程
>
>    配置cancelToken对象
>
>    缓存用于取消请求的cancel函数
>
>    在后面特定时机调用cancel函数取消请求
>
>    在错误回调中判断如果error是cancel, 做相应处理
>
> 2. 实现功能
>
>    点击按钮, 取消某个正在请求中的请求





------





# 跨域

### 同源策略

- 同源策略(Same-Origin Policy)最早由 Netscape 公司提出，是浏览器的一种安全策略。
- 同源： 协议、域名、端口号 必须完全相同。
- 违背同源策略就是跨域。

### 如何解决跨域

#### 1.JSONP

- JSONP是什么

  JSONP(JSON with Padding)，是一个非官方的跨域解决方案，纯粹凭借程序员的聪明才智开发出来，只支持get请求。

- JSONP怎么工作的？

  在网页有一些标签天生具有跨域能力，比如：img link iframe script。

  JSONP就是利用script标签的跨域能力来发送请求的。

- JSONP的使用

  - 动态的创建一个script标签

    `var script = document.createElement("script");`

  - 设置script的src，设置回调函数

    `script.src = "http://localhost:3000/testAJAX?callback=abc";`

    `function abc(data) {alert(data.name);};`

  - 将script添加到body中

    document.body.appendChild(script);

  - 服务器中路由的处理

    ```js
    router.get("/testAJAX" , function (req , res) {
        console.log("收到请求");
        var callback = req.query.callback;
        var obj = {
            name:"孙悟空",
            age:18
        }
        res.send(callback+"("+JSON.stringify(obj)+")");
    });Copy to clipboardErrorCopied
    ```

- jQuery中的JSONP

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body> 
        <button id="btn">按钮</button>
        <ul id="list"></ul>
        <script type="text/javascript" src="./jquery-1.12.3.js"></script>
        <script type="text/javascript">
            window.onload = function () {
                var btn = document.getElementById('btn') btn.onclick = function () {
                    $.getJSON("http://api.douban.com/v2/movie/in_theaters?callback=?", function (data) {
                        console.log(data);
                        * //获取所有的电影的条目*          var subjects = data.subjects;           *//遍历电影条目*          for(var i=0 ; i<subjects.length ; i++){             $("#list").append("<li>"+               subjects[i].title+"<br />"+               "<img src=\""+subjects[i].images.large+"\" >"+               "</li>");           }         });       }     }   
        </script>
    </body>

  </html>Copy to clipboardErrorCopied
```

#### 2.CORS

- CORS是什么？

  CORS（Cross-Origin Resource Sharing），跨域资源共享。CORS是官方的跨域解决方案，它的特点是不需要在客户端做任何特殊的操作，完全在服务器中进行处理，支持get和post请求。

- CORS怎么工作的？

  CORS是通过设置一个响应头来告诉浏览器，该请求允许跨域，浏览器收到该响应以后就会对响应放行。

- CORS的使用

  主要是服务器端的设置：

  ```js
  router.get("/testAJAX" , function (req , res) {
  //通过res来设置响应头，来允许跨域请求
  //res.set("Access-Control-Allow-Origin","http://127.0.0.1:3000");  
  res.set("Access-Control-Allow-Origin","*");
  res.send("testAJAX返回的响应");
  });
  ```



#### 3.代理跨域（未完待续……）







