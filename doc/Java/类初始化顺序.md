# 类的初始化顺序

## 1. 单个类的初始化

```java
class Foo {
    static {
        // [1]
    }
    
    static Foo2 obj = new Foo2(); // [3]
    
    Foo2 obj2 = new Foo2(); // [4]
    
    public Foo() {
        // [5]
    }
}

class Foo2 {
    static {
        // [2]
    }
}
```

如上面代码所示，加载顺序如备注标记的一样。

1. 先加载这个类的 static 代码块
2. 加载 Foo2 的 static 代码块（因为要初始化 Foo 的静态成员 obj）
3. 再加载 static 成员变量 obj
4. 再加载普通成员变量 obj2
5. 最后执行构造方法

这里需要注意的是，如果对象 obj 初始化为 null，则不会执行 [2] 的位置。

```java
class Foo {
    static {
        // [1]
    }
    
    static Foo2 obj = null;
    
    Foo2 obj2 = null; 
    
    public Foo() {
        // [2]
    }
}

class Foo2 {
    
}
```

如上面代码所示，如果成员变量都被赋值为 null，则不会调用类的初始化逻辑。

## 2. 继承关系中类的初始化

在继承关系中，不过是先执行父类的初始化，再执行子类的初始化。

但这也就会发生一个怪事（面试点），在执行父类初始化的时候，读取子类的成员变量，自然就会出现 null 了。

比如下面这个例子：

```java
class Animal {
    private String name = "Animal";
    
    public Animal() {
        print();
    }
    
    public void print() {
        System.out.println(name);
    }
}

class Dog extends Animal {
    private String name = "Dog";
    
    public void print() {
        System.out.println(name);
    }
}

public void static main(String[] args) {
    Animal a = new Dog();
}
```

请问输出是什么？

答案是：null

为什么呢？

首先记住大原则就好了，先执行父类的初始化，再执行子类的初始化。

我在代码上加上执行标记。

```java
class Animal {
    // [2] 执行 Animal 的 static block，可惜没有

    private String name = "Animal"; // [3] 执行父类的成员变量初始化
    
    public Animal() { 
        // [4] 执行构造函数
        print();
    }
    
    public void print() {
        System.out.println(name);
    }
}

class Dog extends Animal {
    // [3] 执行 Animal 的 static block，可惜没有

    private String name = "Dog";
    
    public void print() {
        // [5] 这个方法 override 了父类的，所以执行这个 print
        // 由于 Dog.name 没有初始化，所以是null
        System.out.println(name);
    }
}

public void static main(String[] args) {
    Animal a = new Dog(); // [1] 先执行这里
}
```

需要额外注意的地方是：
* 父类和子类都定义了 name ，它的修饰符是 private，因此这两个是独立的变量，没有任何关联。
* print 方法是 public 的，因此在集成关系上有 override 的关系（有些情况下不加 @Override 注解被认为不是 override，因此规范起见，最好加上 @Override）