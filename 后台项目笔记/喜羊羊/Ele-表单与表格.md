# 表单

```js
:model	是绑定我们form表单中需要提交给后台的一个对象

:rules	是动态绑定的rules，表单验证规则

ref		是我们绑定的对象
```



```js
然后在每一个el-form-item上面绑定prop属性，这个属性就是我们下面要做正则校验的地方，而下面所绑定的值，都是在ruleForm这个对象中
```



```js
select与option必须有value属性（v-model本质是value+oninput）

select的v-model要收集什么option中的value就写什么
```



```js
Select组件中有比watch更好的手段
即@change事件	选中值发生变化时触发	回调参数是：目前的选中值
```



------

# 表格



> 表格是以列为基础的



```js
table中
:data="数组"
```



```js
序号列  
type="index"
align="center"
```



```js
prop="遍历的属性"

```



**展示一个数据，要认清其结构**

若需要展示的数据还要深挖一层数组结构，则使用 作用域插槽拿到当前遍历的row（看data写的数组，也可以简单理解为item），进行又一层的遍历



```js
<el-table style="width: 100%" border :data="attrList">
          <el-table-column label="序号" width="80" type="index" align="center">
          </el-table-column>

          <el-table-column prop="attrName" label="属性名称" width="150">
          </el-table-column>

          <el-table-column label="属性值列表" width="width">
            <template v-slot="{ row, $index }">
              <el-tag
                type="danger"
                v-for="(attrValue, index) in row.attrValueList"
                :key="attrValue.id"
              >
                {{ attrValue.valueName }}
              </el-tag>
            </template>
          </el-table-column>
          ...
</el-table>
```













































