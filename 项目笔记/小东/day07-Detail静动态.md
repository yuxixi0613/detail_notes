## 1. 静态 路由组件Search 编程式路由导航

1. Search路由组件 商品图片和详情跳转
   1. router-link to '/detail' + goods.id  '/detail' + goods.id

```html
<div class="p-img">
    <router-link :to="'/detail/'  + goods.id">
      <img :src="goods.defaultImg" />
    </router-link>
</div>

<div class="attr">
    <router-link :to="'/detail/' + goods.id">
      {{goods.title}}
    </router-link>
</div>
```

2. 捞静态 Detail路由组件 至 Pages

3. Deatil路由组件注册 routes.js配置路径 /detail:skuId
   1. spu skuId 商品ID 切记携带一个params参数Id区分不同商品

```js
    {
        path: '/detail/:skuId',
        component: Detail,
    },
```

4. 测试：图片点击和title详情 走你

## 2. 配置路由滚动行为

1. [Vue Router v3.x 滚动行为 异步滚动]: https://v3.router.vuejs.org/zh/guide/advanced/scroll-behavior.html#%E5%BC%82%E6%AD%A5%E6%BB%9A%E5%8A%A8	"跳转后垂直滚动条不置顶问题"

   配置路由的固定项

   ```js
   export default new VueRouter({
       routes,
       // 下面这个函数以后是配置路由的固定项
       scrollBehavior (to, from, savedPosition) {
           return { x: 0, y: 0 }
       }
   })
   ```

2. 再次测试：走你

## 3.  初始化动态数据渲染

> 初始化动态渲染：api store vuex三连环 发请求 拿数据 展示

1. 查看api前台接口文档 查看获取商品详情

   请求地址 请求方式 参数类型 返回示例

2. api中书写 商品详情信息 接口请求函数

```js
export const reqGoodsDetailInfo = (skuId) => {
    return request({
        url: `/item/${ skuId }`,
        method: 'get'
    })
}
```

3. store moudule下新建 detail.js 大store中引入并合并

```js
import { reqGoodsDetailInfo } from '@/api'

const state = {
    goodsDetailInfo: {}
}
const mutations = {
    RECEIVE_GOODSDETAILINFO(state,goodsDetailInfo){
        state.goodsDetailInfo = goodsDetailInfo
    }
}
const actions = {
    async getGoodsDetailInfo({commit},skuId){
        try {
            const result = await reqGoodsDetailInfo(skuId)
            if(result.code === 200){
                commit('RECEIVE_GOODSDETAILINFO', result.data)
            }else{
                alert('获取商品详情失败')
            }
        } catch (error) {
            alert('请求获取商品详情失败')
        }
    }
}
const getters = {}

export default {
    state,
    mutations,
    actions,
    getters
}
```

4. Detail路由组件 发送dispatch请求

   mounted methods (data return skuId )beforeMount

```js
  export default {
    name: 'Detail',
    data(){
      return {
        skuId: '',
      }
    }
    components: {
      ImageList,
      Zoom
    },
    beforeMount(){
      this.skuId = this.$route.params.skuId;
    },
    mounted(){
      this.getGoodsDetailInfo()
    },
    methods: {
      getGoodsDetailInfo() {
        this.$route.dispath('getGoodsDetailInfo', this.skuId)
      }
    }
  }
```

5. store module detail.js 三连环中 getters

   categoryView(state) skuInfo(state) spuSaleattrList(state)

```js
const getters = {
    categoryView(state){
        return state.goodsDetailInfo.categoryView || {}
    },
    skuInfo(state){
        return state.goodsDetailInfo.skuInfo || {}
    },
    spuSaleAttrList(state){
        return state.goodsDetailInfo.spuSaleAttrList || {}
    }
}
```

6. Detail路由组件 计算属性 计算 computed ...mapGetters

```js
    computed: {
      ...mapGetters(["categoryView", "skuInfo", "spuSaleAttrList"]),
    }
```

7. Vue 开发者工具查看 Detail路由组件  vuex bindings 是否有数据

## 3. 动态数据渲染：面包屑 商品的信息(商品图片) 销售属性信息

1. Detail路由组件 面包屑

```html
      <!-- 导航路径区域 -->
      <div class="conPoin">
        <span>{{categoryView.category1Name}}</span>
        <span>{{categoryView.category2Name}}</span>
        <span>{{categoryView.category3Name}}</span>
      </div>
```

2. 商品的信息(商品图片) 名字 描述 价格 skuInfo .skuName .skuDesc .price

```html
<!-- 右侧选择区域布局 -->
<div class="InfoWrap">
  <div class="goodsDetail">
    <h3 class="InfoName">{{skuInfo.skuName}}</h3>
    <p class="news">{{skuInfo.skuDesc}}</p>
    <div class="priceArea">
      <div class="priceArea1">
        <div class="title">价&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;格</div>
        <div class="price">
          <i>¥</i>
          <em>{{skuInfo.price}}</em>
          <span>降价通知</span>
        </div>
```

3. 销售属性

   销售属性 saleAttr sapuSaleAttrList 

   销售属性值 saleAttrValue saleAttr.spuSaleAttrValueList

```html
  <dl v-for="(saleAttr, index) in spuSaleAttrList" :key="saleAttr.id">
    <dt class="title">{{saleAttr.saleAttrName}}</dt>
    <dd
      changepirce="0"
      class="active"
      v-for="(saleAttrValue, index) in saleAttr.spuSaleAttrValueList"
      :key="saleAttrValue.id"
    >{{saleAttrValue.saleAttrValueName}}</dd>
  </dl>
```

4. 测试：页面属性 走你

## 4. 商品信息图片区域展示

1. UI三套图(1x,2x,3x) skuImageList 小图 中图 大图
2. Detail组件 计算属性 ImageList

```js
  computed: {
    ...mapGetters(["categoryView", "skuInfo", "spuSaleAttrList"]),
    ImageList(){
      return this.skuInfo.skuImageList || []
    },
  }
```

3. 父组件Detail中将ImageList 传给 子组件Zoom(放大镜) ImageList(小图)

```html
    <div class="previewWrap">
      <!--放大镜效果-->
      <Zoom :imageList="imageList"/>
      <!-- 小图列表 -->
      <ImageList :imageList="imageList"/>
    </div>
```

4. 组件ImageList props接收传值后 模板动态数据渲染

```html
    <div class="swiper-wrapper">
      <div class="swiper-slide" v-for="(image, index) in imageList" :key="image.id">
        <img :src="image.imgUrl">
      </div>
    </div>
```

5. 组件Zoom接收传值后 data中定义currentIndex  模板动态数据渲染

   computed currentImage 计算展示的图片 防止假报错

```js
  export default {
    name: "Zoom",
    props: ['imageList'],
    data(){
      return {
        currentIndex: 0 //为什么要定义这个数据，是因为图标索引不能写死
      }
    },
    computed: {
      // 为了要去计算展示的图片，是为了防止假报错
      currentImage(){
        return this.imageList[this.currentIndex] || {}
      }
    },
  }
```

```html
  <div class="spec-preview">
    <!-- <img :src="imagesList[0].imgUrl" /> -->
    <img :src="currentImage.imgUrl" />
    <div class="event"></div>
    <div class="big">
      <img :src="currentImage.imgUrl" />
    </div>
    <div class="mask"></div>
  </div>
```

6. 测试：查看页面初始化动态数据渲染 走你

## 5. 交互 销售属性值的排他思想

1. Detail 路由组件 :class @click changeIsCheck

```html
    <dd
      changepirce="0"
      :class="{active:saleAttrValue.isChecked === '1'}"
      v-for="(saleAttrValue, index) in saleAttr.spuSaleAttrValueList"
      :key="saleAttrValue.id"
      @click="changeIscheck(saleAttr.spuSaleAttrValueList,saleAttrValue)"
    >{{saleAttrValue.saleAttrValueName}}</dd>
```

```js
  methods: {
    getGoodsDetailInfo() {
      this.$store.dispatch("getGoodsDetailInfo", this.skuId);
    },
    // 点击销售属性值排它
    changeIscheck(spuSaleAttrValueList,saleAttrValue){
      spuSaleAttrValueList.forEach(item => item.isChecked = '0')
      saleAttrValue.isChecked = '1'
    }
  },
```

## 6. 小图 中图 同步切换

1. 小图之间切换的边框 :class currentIndex === index @click  changeIndex

```html
  <div class="swiper-slide" v-for="(image, index) in imageList" :key="image.id">
    <img :src="image.imgUrl" :class="{ active: currentIndex === index}"
    @click="changeIndex(index)" />
  </div>
```

2. 参考值 data currentIndex

   小图切换 中图同步切换 ImageList和Zoom兄弟组件用 全局事件总线

```js
export default {
  name: "ImageList",
  props: ["imageList"],
  data() {
    return {
      currentIndex: 0,
    };
  },
  methods: {
    changeIndex(index){
      this.currentIndex = index;
      this.$bus.$emit("changeIndex", index);
    }
  }
};
```

3. 兄弟组件 Zoom

```js
      mounted(){
        this.$bus.$on('changeIndex', this.changeIndex)
      },
      methods: {
        // 点击小图传递点击的小图下标，通过全局事件总线
        changeIndex(index){
          this.currentIndex = index
        }
      }
```

## 7. 小图轮播

1. ImagesList组件 Swiper 去非路由组件SliderLoop拷走watch 监视imagesList

<div class="swiper-container" ref="imgSwiper">
2. [Swiper Carouser旋转木马]: https://www.swiper.com.cn/api/carousel/24.html

   sliderPerView每屏显示几个图 

   sliderPerGroup几个小图一组 滑动 loop干掉小 图轮播图over


```js
  watch: {
    ImageList: {
      immediate: true,
      handler() {
        if (this.imageList.length === 0) return;
        // console.log("$nextTick被调用了"); //调用4次 初始化1次 2个floor 1次变化
        this.$nextTick(() => {
          new Swiper(this.$refs.imgSwiper, {
            // 如果需要分页器
            pagination: {
              el: ".swiper-pagination"
            },

            slidesPerView: 3, //每屏显示几个图
            slidesPerGroup: 3, //几个图一组滑动

            // 如果需要前进后退按钮
            navigation: {
              nextEl: ".swiper-button-next",
              prevEl: ".swiper-button-prev"
            }
          });
        });
      }
    }
  }
```

## 8. 放大镜逻辑

> 放大镜的步骤： 
>
> ​	a.鼠标动 遮罩层动 b. 遮罩层动 大图动 c. 大图 动的 反向2倍

1. Zoom组件 event methods

```html
<template>
  <div class="spec-preview">
    <!-- <img :src="imagesList[0].imgUrl" /> -->
    <img :src="currentImage.imgUrl" />
    <div class="event" @mousemove="move"></div>
    <div class="big">
      <img :src="currentImage.imgUrl" ref="bigImg" />
    </div>
    <div class="mask" ref="mask"></div>
  </div>
</template>
```

```js
    move(event) {
      // 1、鼠标动、遮罩动
      let mask = this.$refs.mask;
      let bigImg = this.$refs.bigImg;

      let mouseX = event.offsetX;
      let mouseY = event.offsetY;

      let maskWidth = mask.offsetWidth;
      let maskHeight = mask.offsetHeight;

      let maskX = mouseX - maskWidth / 2;
      let maskY = mouseY - maskHeight / 2;

      // 判断横向边界
      if (maskX < 0) {
        maskX = 0;
      } else if (maskX > maskWidth) {
        maskX = maskWidth;
      }

      // 判断纵向边界
      if (maskY < 0) {
        maskY = 0;
      } else if (maskY > maskHeight) {
        maskY = maskHeight;
      }

      mask.style.left = maskX + "px";
      mask.style.top = maskY + "px";

      bigImg.style.left = -2 * maskX + "px";
      bigImg.style.top = -2 * maskY + "px";
    }
```

## 9. 购买数量的操作

1. Detail组件 商品数量 v-model skuNum + @click skuNum+=1 - @click skuNum=skuNum <=1?1:skuNum-1

```js
  data() {
    return {
      skuId: "",
      skuNum: 1
    };
  },
```

```html
  <div class="controls">
    <input autocomplete="off" class="itxt" v-model="skuNum" />
    <a href="javascript:" class="plus" @click="skuNum+=1">+</a>
    <a href="javascript:" class="mins" @click="skuNum=skuNum<=1?1:skuNum-1">-</a>
  </div>
```

2. 失去焦点事件：blur事件 change改变事件 

   判断内容新值和老值是否一样 不一样触发

   @change skuNum = skuNum >= 1? parseInt(skuNum):1

   逻辑判断

   ​	1. 小数取整

   ​	2. 应该包含 输出字符串转为NaN情况 直接为 1

```html
  <div class="controls">
    <input autocomplete="off" class="itxt" v-model="skuNum" @change="skuNum = skuNum >= 1?parseInt(skuNum):1" />
    <a href="javascript:" class="plus" @click="skuNum+=1">+</a>
    <a href="javascript:" class="mins" @click="skuNum=skuNum<=1?1:skuNum-1">-</a>
  </div>
```

## 10. 请求添加购物车 点击添加购物车书写api和三连环

1. 添加购物车绑定点击方法 @click addShopCart methods中定义addShopCart(){}

   a. 发请求 构造购物车数据 传到后台 后台存储

   b. 接收后台返回响应，再根据响应结果判断是否跳转

   c. 书写api请求函数 reqAddOrUpdateShopCart
   
   d. Vuex三连环 store modules shopcart.js addOrUpdateShopCart

Detail组件 加入购物车绑定 点击事件

```html
  <div class="add">
    <a href="javascript:" @click="addShopCart">加入购物车</a>
  </div>
```



api index.js 接口请求函数 reqAddOrUpdateShopCart(skuId, skuNum) 接口查文档

```js
// 添加购物车
export const reqAddOrUpdateShopCart = (skuId,skuNum)=> {
    return request({
        url: `/cart/addToCart/${ skuId }/${ skuNum }`,
        method: `post`
    })
}
```

Vuex三连环 store moudule下新建 shopcart.js 大store中引入其并合并

```js
import { reqAddOrUpdateShopCart } from '@/api'

const state = {}
const mutations = {}

/* 
    async函数 异步函数
    - 特点：内部一般都是异步操作
    - async 函数返回值返回promise，不看return
    - 返回的promise是成功还是失败看return

    return 结果不一样
    - return的如果是正常值或者不写 返回的promise都是成功的 成功的结果就是return的结果
    - return的如果是失败的promise，那么返回的promise就是失败的，失败的原因就是return的promise失败的原因
    - return的如果是成功的promise，那么返回的promise就是成功的，成功的结果就是return的promise成功的结果
    - 如果抛出异常，那么返回的promise就是失败的，失败的原因就是抛出的异常原因

*/

// 什么时候action函数当中要写return
// 当我们dispatch后，需要根据请求的成功和失败有后续操作，就要写return
const actions = {
    async addOrUpdateShopCart({commit},{skuId,skuNum}){
        try {
            const result = await reqAddOrUpdateShopCart(skuId,skuNum)
            if(result.code === 200){
                return 'ok'
            }else{
                return Promise.reject('failed')
            }
        } catch (error) {
            // 失败的promise可以让作用域链继续往下传
            return Promise.reject('failed')
        }
    }
}
const getters = {}

export default {
    state,
    mutations,
    actions,
    getters
}
```

2. Detail组件 函数完善 async addShopCart try...catch await this.$store.dispatch 分发action

```js
    // 点击添加购物车
    async addShopCart(){
      let {skuId,skuNum} = this
      // 1、发请求，目的是把当前这个商品构造成购物车数据，传递给后台，后台需要把购物车存储到数据库
      // 存储之后才叫添加购物车成功，后台添加成功之后会返回我们响应
      try {
        // 代表添加成功
        // 相当于是在调用我们store当中的async函数，返回值一定是promise
        await this.$store.dispatch('addOrUpdateShopCart',{skuId,skuNum})
        alert('添加购物车成功，准备自动跳转到添加购物车成功页面')
      }
    }
```

3. 捞静态组件 添加到购物车成功_静态 AddCartSuccess 至 pages
4. router routes.js 配置路由 路径与组件

```js
    {
        path: '/addcartsuccess',
        component: AddCartSuccess,
    },
```

​	5. 简单数据 路由传参 无论params或query

​	复杂数据 别用路由参数 采用存储方案解决

​	路由传参 '/addcartsucess?skuNum=' + skuNum

​	商品数量使用路由传参 商品的信息对象使用存储方案

```js
    getGoodsDetailInfo() {
      this.$store.dispatch("getGoodsDetailInfo", this.skuId);
    },
    // 点击销售属性值排它
    changeIscheck(spuSaleAttrValueList, saleAttrValue) {
      spuSaleAttrValueList.forEach(item => (item.isChecked = "0"));
      saleAttrValue.isChecked = "1";
    },
    // 点击添加购物车
    async addShopCart(){
      let {skuId,skuNum} = this
      // 1、发请求，目的是把当前这个商品构造成购物车数据，传递给后台，后台需要把购物车存储到数据库
      // 存储之后才叫添加购物车成功，后台添加成功之后会返回我们响应
      try {
        // 代表添加成功
        // 相当于是在调用我们store当中的async函数，返回值一定是promise
        await this.$store.dispatch('addOrUpdateShopCart',{skuId,skuNum})
        alert('添加购物车成功，准备自动跳转到添加购物车成功页面')
        /* 
          - 如果以后路由跳转需要传参，数据如果是简单数据，就采用路由传参即可
          - 无论是params还是query都行
          - 如果以后路由跳转需要参数，数据如果是复杂数据，就别用路由传参，使用储存方案解决
          - 如果使用路由传参是会出现问题，因为对象最终要转化为字符串，我们必须事先使用
          - Json.stringify转化为字符串去带回去，它的长度可能很长，如果超出url地址栏长度限定，那么会自动截取

          - localStorage和sessionStorage
          - 永久存储和临时存储
          - 他们两个在存储对象数据的时候都会隐式转化为字符串进行存储
          - 对象转字符串 [object object]
          - 数组转字符串 去掉中括号加引号
          - 函数转字符串 函数体直接加引号

          - 最终，商品的数量使用路由传参
          - 商品的信息对象使用存储方案
        
        */

       this.$router.push(`/addcartsuccess`)
      } catch (error) {
        // 代表添加失败
        alert('添加购物车失败，留在原地')

        // 2、接受到后台返回的响应之后，再根据响应的结果决定跳还是不跳
      }
    }
```

6.  MDN localStorage  window.localStorage Web API

   https://developer.mozilla.org/zh-CN/docs/Web/API/Window/localStorage

   下面的代码片段访问了当前域名下的本地 [`Storage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage) 对象，并通过 [`Storage.setItem()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage/setItem) 增加了一个数据项目。

   ```js
   localStorage.setItem('myCat', 'Tom');
   ```

   该语法用于读取 `localStorage` 项，如下:

   ```js
   let cat = localStorage.getItem('myCat');
   ```

   该语法用于移除 `localStorage` 项，如下:

   ```js
   localStorage.removeItem('myCat');
   ```

   该语法用于移除所有的 `localStorage` 项，如下:

   ```js
   // 移除所有
   localStorage.clear();
   ```

   localStorage和sessionStorage 永久存储和临时存储

   他们两个在存储对象数据的时候都会隐式转化为字符串进行存储

   对象转字符串 [object object]

   数组转字符串 去掉中括号加引号

   函数转字符串 函数体直接加引号

   最终，商品的数量使用路由传参

    商品的信息对象使用存储方案

   sessionStorage.setItem('skuInfo_key',JSON.stingify(skuInfo))}

```js
// 点击添加购物车
async addShopCart(){
  let {skuId,skuNum,skuInfo} = this
  // 1、发请求，目的是把当前这个商品构造成购物车数据，传递给后台，后台需要把购物车存储到数据库
  // 存储之后才叫添加购物车成功，后台添加成功之后会返回我们响应
  try {
    // 代表添加成功
    // 相当于是在调用我们store当中的async函数，返回值一定是promise
    // const result = await this.$store.dispatch('addOrUpdateShopCart',{skuId,skuNum})
    // console.log(result);

    await this.$store.dispatch('addOrUpdateShopCart',{skuId,skuNum})
    alert('添加购物车成功，准备自动跳转到添加购物车成功页面')
    /* 
      - 如果以后路由跳转需要传参，数据如果是简单数据，就采用路由传参即可
      - 无论是params还是query都行
      - 如果以后路由跳转需要参数，数据如果是复杂数据，就别用路由传参，使用储存方案解决
      - 如果使用路由传参是会出现问题，因为对象最终要转化为字符串，我们必须事先使用
      - Json.stringify转化为字符串去带回去，它的长度可能很长，如果超出url地址栏长度限定，那么会自动截取

      - localStorage和sessionStorage
      - 永久存储和临时存储

      - 他们两个在存储对象数据的时候都会隐式转化为字符串进行存储
      - 对象转字符串 [object object]
      - 数组转字符串 去掉中括号加引号
      - 函数转字符串 函数体直接加引号

      - 最终，商品的数量使用路由传参
      - 商品的信息对象使用存储方案

    */
   sessionStorage.setItem('skuInfo_key',JSON.stringify(skuInfo))

   this.$router.push(`/addcartsuccess?skuNum=` + skuNum)
  } catch (error) {
    // 代表添加失败
    alert('添加购物车失败，留在原地')

    // 2、接受到后台返回的响应之后，再根据响应的结果决定跳还是不跳
  }
}
```

## 11. 请求成功后跳转到添加购物车成功界面渲染添加成功页面的数据

1. AddCartSuccesss组件data return skuNum: '',skuInfo: {}

 beforeMounted this.skuNum = this.$route.query.skuNum

 this.skuInfo = JSON.parse(sessionStorage.getItem('skuInfo_key'))

```js
  export default {
    name: 'AddCartSuccess',
    data(){
      return {
        skuNum: '',
        skuInfo: {}
      }
    },
    beforeMount(){
      this.skuNum = this.$route.query.skuNum
      this.skuInfo = JSON.parse(sessionStorage.getItem('skuInfo_key'))
    }
  }
```

2. 模板动态数据 :src skuInfo.skuDefaultImg skuInfo.skuName skuNum

```html
<div class="left-good">
  <div class="left-pic">
    <img :src="skuInfo.skuDefaultImg">
  </div>
  <div class="right-info">
    <p class="title">{{skuInfo.skuName}}</p>
    <p class="attr">颜色：WFZ5099IH/5L钛金釜内胆 数量：{{skuNum}}</p>
  </div>
</div>
```
3.  辉洪 fonts 捞入 public font.css捞入css reset.css 第一行解掉注释

   商品已成功加入购物车前面 绿色勾勾就图标就出来了



