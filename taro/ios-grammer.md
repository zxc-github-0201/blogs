### object-c 的一些语法

- alloc 返回类的实例，常用`TheClass *newObject = [[TheClass alloc] init];`，定义类的时候将初始化方法放在 init 中，不要重写 alloc。alloc 内不会分配内存，同 C 的 alloc

-
