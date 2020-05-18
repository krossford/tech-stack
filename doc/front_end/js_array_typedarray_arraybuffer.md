# JS 中的 Array、TypedArray 和ArrayBuffer

## Array
JS 中的 Array 是一个类似于列表的实现，它可以增加元素和删掉元素，如 `push`, `pop`。最大的特性是动态的，并且可以存储任何类型的数据。

```js
let arr = [];
arr.push("abc");

let arr2 = [];
arr2.push(100);
```

## TypedArray

TypedArray 是一系列指定了每个元素大小的数组，如：`Uint8Array`，说的是这个数组里面，每个元素都是 8 位的 uint 值。

TypedArray 有很多种类型，名字里面的数组表示的都是 bit 数。

```javascript
Int8Array();
Uint8Array();
Uint8ClampedArray();
Int16Array();
Uint16Array();
Int32Array();
Uint32Array();
Float32Array();
Float64Array();
BigInt64Array();
BigUint64Array();
```

### 初始化

TypedArray 是不可以增长或者缩减的，因此它无法动态的增加元素，它的 length 总是固定的，决定于初始化的时候。

你可以这样初始化它：

```js
let a = Int8Array(8); // 指定长度
let b = Int8Array([1, 2, 3]); // 或者传入一个 Array
```

### 修改
虽然它无法被伸缩，但是它的值是可以被修改的。

```js
let b = Int8Array([1, 2, 3]);
b[1] = 100;
```

### 溢出
由于每个元素都是有固定的大小，那么如果我传入一个超出范围的值会发生什么呢？

```js
let arr = [1, 256, 1024];
let a = new Int8Array(arr);
console.log(a); // [1, 0, 0] 256, 1024 都溢出了，变成了 0
a[0] = 256;
console.log(a); // [0, 0, 0]
```

## ArrayBuffer

ArrayBuffer 是 TypedArray 的一个只读对象，可以通过这样的方式获取它。

```js
let a = Int8Array([1, 2, 3]);
let buff = a.buffer; // buff 是一个 ArrayBuffer
```

ArrayBuffer 的元素总是一个 byte，因此它可以被理解为 byteArray。

接下来请看下面的代码，并思考一下它的输出是什么？

```js
let a = Int8Array(8);
console.log(a.buffer.length); // 8

let b = Int16Array(8);
console.log(b.buffer.length); // 16
```

由于 ArrayBuffer 总是字节的数组，因此 16 位的数组中的一个元素将被拆成两个填到 ArrayBuffer 中。