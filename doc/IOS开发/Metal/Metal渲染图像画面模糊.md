# IOS Metal渲染图像画面模糊

## <1> 前言

这篇文章想谈一谈 Metal 中的 View Port，屏幕的 scale 和像素以及点的概念与关系，都是这个问题所引出的概念，当具备这些知识后，问题就有了头绪，才能迎刃而解。

## <2> 概念

最先要说的是像素（pixel）和点（point），像素是指屏幕上最小的一个显示单元，

scale 代表多少个像素表示一个点，可以用公式表示为：$point = scale \times pixel$。



## <3> Layer.contentsScale

layer 的默认的 contentsScale 都是 1