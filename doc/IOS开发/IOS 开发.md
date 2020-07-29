# IOS 开发

## <1> 文件

```objectivec
//添加在工程文件最外层使用方式
NSString *path = [[NSBundle mainBundle] pathForResource:@"文件名" ofType:@"后缀"];
//下图1位置获取方式
NSString *path = [[NSBundle mainBundle] pathForResource:@"font" ofType:@"ttf"];
//若你加在了某个文件夹中 依然使用如下方法！！！！！
NSString *path = [[NSBundle mainBundle] pathForResource:@"font2" ofType:@"ttf"];
```

如果文件位于蓝色文件夹，则需要使用 inDirectory 参数指定蓝色文件夹的名字。

读取文件内容

```swift
let fileContent = try? NSString.init(contentsOf: URL(fileURLWithPath: Bundle.main.path(forResource: "a", ofType: "CUBE")!), encoding: String.Encoding.utf8.rawValue)
```

## <2> 内存

[Youtube: Exploring Swift Memory Layout](https://www.youtube.com/watch?v=ERYNyrfXjlg)

[https://swiftunboxed.com/internals/size-stride-alignment/#:~:text=Size%20is%20the%20number%20of,next%20item%20in%20the%20buffer.](https://swiftunboxed.com/internals/size-stride-alignment/#:~:text=Size is the number of,next item in the buffer.)

