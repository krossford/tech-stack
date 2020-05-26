# VGA 是如何工作的？

## 介绍

VGA 是 Video Graphics Adapter 或者 Video Graphics Array 的缩写，它是一种视频显示技术的标准。实现这个标准的接口就是那个蓝色的一大坨需要两个螺丝的视频接口，如下图所示：

![](../img/img_vga.jpg)

上图所示的两个接口中，左边那个全是针的，用来插别人，通俗来说，我们就叫它 **VGA 公**，另一个全是洞洞，被别人插，就叫 **VGA 母**。虽然很邪恶，但事实上就是这么叫的，我好奇的搜了一下英文中的称呼，英文中使用 Male 和 Female 区分插与被插的关系，我觉得真是相当贴切。

VGA 总共有 15 个针脚，排列为 3 行，每行 5 个。如下图：

![](../img/img_vga_port.png)

每个针（pin）都有特定的功能，详细的功能描述如下表，表格来源：[这里](https://pinouts.ru/Video/VGAVesaDdc_pinout.shtml)

![](../img/img_vga_pin_table.png)

现在我们主要关注 pin 1, 2, 3, 13, 14，它们分别是表示颜色的 RGB 和水平同步、垂直同步信号。

## VGA 是如何显示视频数据的？

想一想我们有一个显示器，有多少个像素，一个导线通电告诉我们亮还是暗

三个导线决定颜色

但是这么多像素，怎么决定所有的颜色呢

其中一种办法是时分，在一定的时间内显示完整个画面，将每个像素的信息分散到这个周期里面

需要水平和垂直同步信号来换行和刷新。

时序 timing

front porch 和 back front proch 的作用

### ColorBurst 是什么

## 为什么要有两个螺丝

## 参考资料

* [How exactly does a VGA cable work?](https://electronics.stackexchange.com/questions/166681/how-exactly-does-a-vga-cable-work)
* [VGA Adapter](http://www.eecg.utoronto.ca/~jayar/ece241_06F/vga/index.html)
* [Introduce to VGA](http://media.ee.ntu.edu.tw/personal/pcwu/dclab/dclab_10.pdf)
* [what-is-front-porch-and-back-porch-of-a-video-signal-in-crt-display](https://electronics.stackexchange.com/questions/201011/what-is-front-porch-and-back-porch-of-a-video-signal-in-crt-display/201014#201014)
* [Horizontal blanking interval](https://en.wikipedia.org/wiki/Horizontal_blanking_interval)
* [Colorburst](https://en.wikipedia.org/wiki/Colorburst)
* [Analog television](https://en.wikipedia.org/wiki/Analog_television)