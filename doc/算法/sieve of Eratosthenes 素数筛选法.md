# sieve of Eratosthenes 素数筛选法

## 前置知识
在阅读此文档前，你需要知道：
* [素数与合数](../math/prime_number_and_composite_number.md)

## sieve of Eratosthenes 素数筛选法是什么？

sieve of Eratosthenes 素数筛选法是一种找出指定范围内所有素数的方法。

现在给出一个问题：如何找到 [0, n] 之间的所有素数？

当然是可以一个个去试的，但是这样是很低效的，因为每个都需要去挨个挨个的整除。

基本的思想是这样的，如果我知道一个数 x 是素数，那么它的倍数一定是合数，也就是 2x, 3x, 4x, ... 都是合数。

举例说明：2 是素数，它的倍数们：4、6、8、10 等等，都是合数，这是显而易见的。

现在可以用伪代码来描述一下这个过程。

```java
input: n; // 找出 [0, n] 之间所有的素数

bool[] mem = new bool[n + 1]; // 初始化一个长度为 n + 1 的数组
fillAll(mem, true); // 将所有的值填充为 true，认为所有的数都是素数

count = 0;
for (i = 2 to sqrt(n)) { // 从 2 开始迭代到 sqrt(n)
    if (mem[i]) {
        count++; // 发现素数，count 自增

        // 并将后面的倍数都标记为 false
        for (j = i * 2; j < n + 1; j += i) { 
            mem[i] = false;
        }
    }
}

output: count; // count 就是结果
```

## 相关leetcode
* [204. 计数质数](https://leetcode-cn.com/problems/count-primes/)