# CSS

目录
* [定义](#定义)
* [选择器](#选择器)
    * [简单选择器](#简单选择器)
    * [组合选择器](#组合选择器)
    * [Pseudo Classes Selector](#Pseudo&#160;Classes&#160;Selector)


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

<details>
<summary>通过标签名进行选择</summary>

```css
/* 将选中所有的 <p> 标签 */
p { ... }

/* 将选中所有的 <h1> 标签 */
h1 { ... }
```
</details>

<details>
<summary>通过 id 和 class 进行选择</summary>

```css
/* 选择某个标签的 id 为 myId 的标签，如：<p id="myId"> */
#myId { ... }

/* 选择标签的 class 为 myClass 的所有标签，如：<p class="myClass"> */
.myClass { ... }
```

</details>

<details>
<summary>标签名和 id、class 名组合在一起进行选择</summary>

```css
/* 选择 <p> 标签中 class 为 myClass 的元素 */
p.myClass { ... }

/* 这种似乎意义不大？ */
p#myId { ... }
```

</details>

<details>
<summary>通配符(*)也可以作为选择器，选中所有元素</summary>

```css
* { ... }
```

</details>

<details>
<summary>多个选择器并列</summary>

```css
h1, h2, p {
    text-align: center;
    color: red;
}
```

</details>

### 组合选择器

组合选择器通过描述元素之间的关系来选择。

<details>
<summary>后代选择器，可以隔代传递</summary>

```css
div p { ... }
```
```html
<div>
    <p>我会被选中</p>
</div>
<div>
    <section>
        <p>我也会</p>
    <section>
</div>
```

</details>

<details>
<summary>Child 选择器，选中直接的后代，不能隔代</summary>

```css
div > p { ... }
```
```html
<div>
    <p>我会被选中</p>
</div>
<div>
    <section>
        <p>我不会被选中了</p>
    <section>
</div>
```

</details>

<details>
<summary>Adjacent Sibling Selector(相邻选择器)</summary>

```css
div + p { ... } /* 跟在 div 后面的一个 p 会被选中 */
```
```html
<div></div>
<p>可以被选中</p>
<p>不能被选中</p>
```

</details>

<details>
<summary>General Sibling Selector</summary>

```css
div ~ p { ... } /* 所有跟在 div 后面的 p 都会被选中 */
```
```html
<div></div>
<p>可以被选中</p>
<a>不能被选中！</a>
<p>可以被选中</p>
```

</details>

### Pseudo Classes Selector

伪类选择器有很多，大体上可以分类一下：

* 与 UI 状态有关
    * :link
    * :visited
    * :hover
    * :active
    * :focus
    * :enabled
    * :disabled
    * :checked
* 与文档结构有关
    * :first-child
    * :nth-child(n)
    * :nth-last-child(n)
    * :nth-of-type(n)
    * :nth-last-of-type(n)
    * :last-child
    * :first-of-type
    * :last-of-type
    * :only-child
    * :only-of-type
    * :root
    * :empty
* 其他
    * :not(x)
    * :target
    * :lang(language)

### Pseudo Elements Selector