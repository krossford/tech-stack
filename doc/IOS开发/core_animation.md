# Core Animation

## 参考资料
* [Core Animation Guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)
  * [Core Animation Basics](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/CoreAnimationBasics/CoreAnimationBasics.html#//apple_ref/doc/uid/TP40004514-CH2-SW3)
  * [Setting Up Layer Objects](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/SettingUpLayerObjects/SettingUpLayerObjects.html#//apple_ref/doc/uid/TP40004514-CH13-SW12)

## 1.概述

Core Animation 并不是一个绘制系统，它只是一个管理 **app 内容**的基础设置，它的核心是称为 layer 的东西，都是通过这个 layer 来管理和操纵 app 的内容。layer 本质上是一个 bitmap，将我们的内容放在里面。通常情况下，都是通过管理 View 的方式来管理 layer，但也可以创建单独的 layer 来进行特殊的操作。

**内容**：这个词在官方文档中以 app contents, contents of views 等形式多次出现，我的理解是，说白了，对于一块全是屏幕的手机设备来说，内容归根结底不就是屏幕上展示的一些信息，这些信息归根结底也不就是 bitmap 嘛。

Core Animation 可以直接通过显卡高效的操作 bitmap。我们可以用 Core Animation 来对 app 的 view 和其他视觉对象做动画，做动画，无非就是连续的对某个属性进行修改，从一个值，修改成另一个值，并要求视觉上立马反馈出修改后的效果。

## 2.Layer

Layer 是整个 Core Animation 的核心，它提供了绘制和动画的基础。其本质是一个在 3D 空间中的 2D 平面。Layer 只管理和 bitmap 有关的信息，而不管理 View 的那一些特性。

基于 bitmap 的绘制和基于 View 的绘制有个最大的区别就是，基于 View 的绘制将使用 CPU 资源和主线程，而基于 bitmap 绘制则使用的是图形处理器。

每一个 View 默认有一个 CALayer 与之绑定，CALayer 有很多特定的子类，可以根据自己的需要创建不同的子类来满足不同的需求。也可以改变 View 默认绑定的 Layer。

### 2.1. CALayer 的子类，改变 UIView 默认的 Layer

* 如果想使用 Metal 或 OpenGL ES，可以使用 CAMetalLayer, CAESGLLayer
* 使用特定的子类提升性能

使用复写 layerClass 来改变默认的 Layer。

下表列出不同的子类及其适用场景：

| class | usage |
|--|--|
| CAEmitterLayer | Used to implement a Core Animation–based particle emitter system. The emitter layer object controls the generation of the particles and their origin.|
| CAGradientLayer | Used to draw a color gradient that fills the shape of the layer (within the bounds of any rounded corners). |
| CAMetalLayer | Used to set up and vend drawable textures for rendering layer content using Metal. |
| CAEAGLLayer/CAOpenGLLayer | Used to set up the backing store and context for rendering layer content using OpenGL ES (iOS) or OpenGL (OS X).|
| CAReplicatorLayer | Used when you want to make copies of one or more sublayers automatically. The replicator makes the copies for you and uses the properties you specify to alter the appearance or attributes of the copies.
| CAScrollLayer | Used to manage a large scrollable area composed of multiple sublayers. |
| CAShapeLayer | Used to draw a cubic Bezier spline. Shape layers are advantageous for drawing path-based shapes because they always result in a crisp path, as opposed to a path you draw into a layer’s backing store, which would not look as good when scaled. However, the crisp results do involve rendering the shape on the main thread and caching the results. |
| CATextLayer | Used to render a plain or attributed string of text. |
| CATiledLayer | Used to manage a large image that can be divided into smaller tiles and rendered individually with support for zooming in and out of the content.|
| CATransformLayer | Used to render a true 3D layer hierarchy, rather than the flattened layer hierarchy implemented by other layer classes.|
| QCCompositionLayer | Used to render a Quartz Composer composition. (OS X only)



### 2.2. 几何

layer 都使用了两套坐标系统，**point-based coordinate system** 和 **unit coordinate system**。

point-based 坐标就是所有的数值都是屏幕上的点。而 unit 坐标，也就是单位坐标，它的坐标轴取值范围永远是从 0 到 1 之间。

## 3.动画

### 3.1.可以做动画的属性

https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/AnimatableProperties/AnimatableProperties.html#//apple_ref/doc/uid/TP40004514-CH11-SW1

### 3.2.如何动画

https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreAnimation_guide/CreatingBasicAnimations/CreatingBasicAnimations.html#//apple_ref/doc/uid/TP40004514-CH3-SW1

### 3.3.自定义动画