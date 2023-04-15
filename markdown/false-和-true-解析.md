# [] == false 和 !![] == true 解析

`==`是相等操作符，先转换再比较；`===`是全等操作符，仅比较不转换。

## `[] == false`

右侧布尔值很简单，直接转换成 `0`

`[]` 不是基本数据类型，它是对象，所以需要调用 `valueOf` 方法（`Array.prototype.valueOf()` ，但是 `Array` 没有实现 `valueOf` 方法，所以根据原型链，最终调用的是 `Object.prototype.valueOf`)：

`[].valueOf()`：得到的仍然是 `[]` ，继续调用 `toString` 方法（`Array.prototype.toString()`）：

`[].toString()`：得到`""`

`""`作为基本数据类型直接调用 `Number("")` 得到 `0`

所以最终结果是 `**true**`

## `!![] == true`

`!!` 比 `==` 的优先级更高，所以先看 `!![]`：

`![]` 得到 `false` （操作数是对象的话逻辑非返回 `false`），然后再取非，得到 `true`

很明显，`true == true` 得到的结果是 `**true**`
