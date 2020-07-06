# IOS Quartz 2D 编程

## 什么是 Graphics Context？

Graphics Context 表示一个绘制区域（drawing destination），它包含了一些绘制参数，包括颜色、clipping area、线宽等等。

## 参考资料
https://developer.apple.com/library/archive/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/dq_context/dq_context.html#:~:text=Glossary-,Graphics%20Contexts,perform%20any%20subsequent%20drawing%20commands.&text=A%20graphics%20context%20is%20represented,is%20an%20opaque%20data%20type

## CGLayer vs CALayer

参考这篇文章：https://stackoverflow.com/questions/4458812/whats-the-difference-and-compatibility-of-cglayer-and-calayer/4594394#

总结一下来说就是，CGLayer 和 CALayer 是完全不相关的两个概念，CGLayer 相当于一个离屏的 bitmap，它可以高效的绘制一个图像，用来**重复使用**。CALayer 则是属于 Core Animation 的模块，是用来在 View 上绘制的。

CALayer 的子类 CAShapeLayer 可以用来绘制基本图形。