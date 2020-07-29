# Metal 开发

在 GPU 中所有执行的代码都被称为 shader，因为历史原因，本来在 GPU 中执行的都是进行着色的任务，所以被称为着色器 shader。但其实也可以做一些别的事情。

## <1> Shader 语法

------

### <1.1> 关键字

#### kernel

标记一个方法是计算方法，这个方法是 public 的，仅可以被我们的 app 看见，没办法被其他 shader 调用。

#### vertex

标记一个方法是 vertex stage 的方法

#### device 
device 表示这个数据是位于这个 device 的地址空间。GPU 可以读写这个内存。

### <1.2> 属性修饰符

#### [[thread_position_in_gird]] 
是系统提供的，

#### [[vertex_id]]
vertex 方法会被调用多次，这个值由系统提供，系统会为每个 vertex 生成一个唯一的值，可以使用此 id 从你的顶点中 index 出一个当前要处理的顶点。

#### [[buffer(index)]]
这个指示符的作用是用来标记 MSL shader 中的参数是哪一个参数，举例说明：

比如在 encoder 中要加入两个参数：

```swift
encoder?.setVertexBytes(arg1, length: ..., index: 0)
encoder?.setVertexBytes(arg2, length: ..., index: 1)
```

那么，我们在 shader 中可以通过 `buffer(index)` 来引用我们添加的参数。

```
vertex RasterizerData vertexShader(uint vertexID [[vertex_id]],
                                   constant MyVertex* vertexs [[buffer(0)]],
                                   constant vector_float2* viewPort [[buffer(1)]])
```

注意上面代码中 buffer(0) 指的是要引用 encoder 传入的 index: 0 的参数，buffer(1) 也是以此类推。

#### [[stage_in]]

这个指示符表明当前的参数来自上个阶段的输出，示例：

```c++
// vertex function return RasterizerData 数据
vertex RasterizerData vertexShader(...)

// fragment function 以上一个 stage 的输出为输入
fragment float4 fragmentShader(RasterizerData in [[stage_in]])
```

#### [[positon]]

position 表示这个修饰符修饰的属性是 clip-space 中的坐标。

#### [[texture(index)]]

同 buffer 一样，是用来指定哪一个 texture 的。

## <2> 对象

### Command Queue

### Command Buffer
将命令存放于此

### Command Encoder
将命令写入到 buffer 中，encoder是具体加入绘制指令和绘制参数的对象。

## <3> 渲染管线 Render Pipeline

------

渲染管线有多个步骤组成（stage），这些 stage 有些是可编程的，有些则是固定的行为。如下图所示，render pipeline 的步骤如下：

其中，vertex function 和 fragment function 是可编程的，rasterization 是固定行为。

![](/Users/kross/Documents/github/tech-stack/doc/IOS开发/metal/metal概念_1.png)

绘制从一个 drawing command 开始，如下：

```swift
renderEncoder.drawPrimitives
```

vertex 阶段提供每个顶点的数据。

raster 阶段决定如何光栅化。

fragment 阶段决定每个像素的值是多少。

vertex 为单个顶点生成数据，fragment 也只为单个片段生成数据，但是我们可以决定要如何处理这些过程。

### <3.1> Vertex Function

```c++
vertex RasterizerData vertexShader(uint vertexID [[vertex_id]],
             constant AAPLVertex *vertices [[buffer(AAPLVertexInputIndexVertices)]],
             constant vector_uint2 *viewportSizePointer [[buffer(AAPLVertexInputIndexViewportSize)]]) 
{
		float2 pixelSpacePosition = vertices[vertexID].position.xy;

		// Get the viewport size and cast to float.
		vector_float2 viewportSize = vector_float2(*viewportSizePointer);
}

```

### <3.2> Fragment Function

![](/Users/kross/Documents/github/tech-stack/doc/ios开发/metal/img_fragment_function.png)

```
fragment float4 fragmentShader(RasterizerData in [[stage_in]])
```

关键字 fragment 表明这是一个 fragment function, 属性修饰符 [[stage_in]] 表示这个参数是由 rasterizer 生成的。

因为只有一个渲染目标，所以返回值是一个 float4 的向量，这个值的意义是颜色值。

## <4> 如何计算出最佳的线程数

------

参考资料：https://developer.apple.com/documentation/metal/calculating_threadgroup_and_grid_sizes

基于两个属性来考虑，第一个是 pipeline 的 `maxTotalThreadsPerThreadgroup`（单个 thread group 所能持有的最大线程数），另一个是 `threadExecutionWidth`(有多少个线程会被派发到 GPU 上去执行)

maxTotalThreadPerThreadgroup 是根据当前的设备和硬件的使用状态来决定的，pipeline 被创建出来之后，max 不会变更了，但是在同一个设备上创建两个 pipeline，他们的 max 可能是不一样的。

假设在一个设备上，max 是 512，width 是 32，一个合适的 threadsPerThreadGroup 是 $width \times (max \div width) $

```swift
let w = pipelineState.threadExecutionWidth
let h = pipelineState.maxTotalThreadsPerThreadgroup / w
let threadsPerThreadgroup = MTLSizeMake(w, h, 1) 
```

这看起来似乎有点异样，$width \times (max \div width) = max$  ，似乎并不需要做什么特别的计算，直接使用 max 就行了。但是这里的都是整数，假设 max = 100，width = 30，那么，最合适的应该是 $30 \times 取整(100 \div 30) = 90$。

## <5> 坐标转换

------

在 Metal 文档中，GPU 的坐标系统被称之为 clip-space coordinate。如下面有图所示。

![image-20200721180118054](/Users/kross/Documents/github/tech-stack/doc/IOS开发/metal/img_coordinate.png)

另一个坐标系统是纹理坐标，纹理坐标是左上角为原点，右下方为正半轴方向的 [0, 1] 之间的规整坐标系。

![纹理坐标系](/Users/kross/Documents/github/tech-stack/doc/IOS开发/Metal/img_texture_coordinate.png)

## <6> 纹理（Texture）

------

纹理本质上是一个结构化的数据集合，比如，图片是一个 2D 的数组，数组元素是颜色值，那么就可以站在纹理的角度说：图片是一个纹理，其结构为 2D 的颜色数组。将纹理绘制到几何图元上的过程称为 texture mapping。fragment function 通过采样的方式来决定每个 fragment 要显示什么颜色。

MTLTexture 表示一个纹理对象，它存储了纹理的类型，结构等各种属性。

MTLTextureDescriptor 表示纹理的一些属性。

### <6.1> 操作

#### a. 创建纹理

 首先要创建一个 MTLTextureDescriptor 对象，然后通过 device.newTextureWithDescriptor 方法来创建。

新创建的纹理内存是未初始化的，所以创建完之后需要我们拷贝图像数据到纹理对象中。

#### b. 更新纹理

通过 MTLRegion 来指定要更新 Texture 的那一部分。



