# CSS

目录
* [定义](#定义)
* [选择器](#选择器)
    * [简单选择器](#简单选择器)
    * [组合选择器](#组合选择器)

## 定义
CSS 的全称是Cascading Style Sheets，中文译名为层叠样式表，用来定义 HTML 页面的样式。

```css
body {
    background-color: lightblue;
}

h1 {
    color: white;
    text-align: center;
}
```

CSS 的语法规则大致可以描述为：
```css
selector {
    property: value
}
```

selector 意为选择器，即选中 HTML 中哪些元素，要应用花括号中的样式。

## 选择器

选择器大体上可以分为 5 类：
* 简单选择器：基于元素的名字、id 或者 class
* 组合选择器：基于元素之间的关系
* 伪类选择器：基于元素特定状态
* 伪元素选择器：基于元素的特定位置
* 属性选择器：基于元素的属性

### 简单选择器

通过**标签名**进行选择。
```css
/* 将选中所有的 <p> 标签 */
p { ... }

/* 将选中所有的 <h1> 标签 */
h1 { ... }
```

通过 **id** 和 **class** 进行选择。
```css
/* 选择某个标签的 id 为 myId 的标签，如：<p id="myId"> */
#myId { ... }

/* 选择标签的 class 为 myClass 的所有标签，如：<p class="myClass"> */
.myClass { ... }
```

标签名和id、class名组合在一起进行选择。
```css
/* 选择 <p> 标签中 class 为 myClass 的元素 */
p.myClass { ... }

/* 这种似乎意义不大？ */
p#myId { ... }
```

**通配符**也可以作为选择器，选中所有元素。
```css
* { ... }
```

写法上，如果多个选择器内的样式是一致的，可以考虑将它们写在一起。
```css
h1, h2, p {
    text-align: center;
    color: red;
}
```

### 组合选择器

组合选择器通过描述元素之间的关系来选择。

**后代选择器**，可以隔代传递。
```css
div p { ... }

<div>
    <p>我会被选中</p>
</div>
<div>
    <section>
        <p>我也会</p>
    <section>
</div>
```

**Child 选择器**，选中直接的后代，不能隔代。
```css
div > p

<div>
    <p>我会被选中</p>
</div>
<div>
    <section>
        <p>我不会被选中了</p>
    <section>
</div>
```

**Adjacent Sibling**