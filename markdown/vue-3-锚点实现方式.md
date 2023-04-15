# Vue 3 锚点实现方式

当我们使用 Vue 3 有时候需要做业内锚点跳转，使用传统的 `a href` 已经不是很推荐了，我们可以尝试采用以下方法来进行替代

## 标题锚点

```html
<div class="cardTagContainer">
    <div class="cardTag" v-for="(cardGroup,index) in card.cardInfo" :key="cardGroup" @click="goAnchor('#tag' + index)">
        {{ cardGroup.title }}
    </div>
</div>
```

## 跳转内容

```html
<h1 :id="'tag' + index">
    {{ cardGroup.title }}
</h1>
```

## 相关方法

```js
// 标题锚点
function goAnchor(selector) {
    // console.log(selector)
    document.querySelector(selector).scrollIntoView({
        behavior: "smooth",
        block: "start"
    });
}
```

## `.scrollIntoView` 属性方法

该 `scrollIntoView()` 方法将调用它的元素滚动到浏览器窗口的可见区域。

PS：根据其他元素的布局，元素可能无法完全滚动到顶部或底部。

_TIPS：页面（容器）可滚动时才有用！_

```js
element.scrollIntoView（）; // 等同于element.scrollIntoView(true)
element.scrollIntoView（alignToTop）; // 布尔参数
element.scrollIntoView（scrollIntoViewOptions）; // 对象参数
```
