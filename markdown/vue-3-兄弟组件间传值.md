# Vue 3 兄弟组件间传值

Vue 3 中兄弟间传值可以使用 Vuex，但小项目使用过于庞大，我们可以使用 mitt 进行兄弟组件间传值。

## 操作步骤

### **第一步：安装 `mitt`**

```js
npm i mitt
```

### **第二步：创建文件（例如：`eventBus.js`）**

```js
import mitt from 'mitt'

const emitter = mitt()
export default emitter
```

### **第三步：将该文件引入至需要进行发送及接受的 `.vue` 文件中**

```js
import emitter from "../untils/bus";
```

### **第四步：发送端写入下列代码**

```js
emitter.emit("response", response)
```

前面是事件，后面是要传入的参数

### **第五步：接收端写入下列代码**

```js
emitter.on("response", (response) => {
    console.log(response)
})
```

这样两个组件间就可以进行传值通信了。
