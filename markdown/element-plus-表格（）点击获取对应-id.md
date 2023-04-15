# Element Plus Table（表格）点击获取对应 id

网上搜到的内容或许适用于 vue 2 或 Element UI，不适用 vue 3 和 Element Plus。

**template：**

```html
<el-table-column label="配置" width="120" align="center">
  <template **v-slot="scope"**>
    <el-button link type="primary" size="small">编辑</el-button>
    <el-button link type="primary" @click="deleteNavigation(scope.row.id)" size="small">删除
    </el-button>
  </template>
</el-table-column>
```

**script：**

```js
function deleteNavigation(id) {
  console.log(id);
}
```

使用 vue 的 `v-slot` 指令，这个指令中是 vue 2.6 新增的，取代了以前的 slot 和 slot-scope，而我使用的是 vue 3，所以网上搜到的很多使用 `slot-scope` 的方法都失效了。

使用 `插槽名.row` 可以获取一行的内容，如果要获取行内的某一个字段的值，只需要再点出它的字段名，在我的代码里，就是 `scope.row.id`。

如果要获取行的索引，使用 `插槽名.$index`，注意$符号是必不可少的，否则将不起作用。
