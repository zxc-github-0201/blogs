### 基本概念与语法

## 消息传递

基本等同于 C++中的函数调用。区别在于，C++中方法与类别对应关系严格，在编译时就已经绑定，无法调用一个不存在类别的方法。但在 Objectc 中，类别与信息比较松散，调用方法试做对物件发送信息，所有的方法都被视为对信息的回应。所有的信息处理直到运行时才决定。

- object-c 代码
  ```object-c
  [obj method: argument];
  ```
- C++ 代码
  ```c++
  obj.method(argument);
  ```

## 类的定义

- C++ 代码

  ```c++
  class MyObject : public NSObject {
    protected:
      int memberVar1;
      void * memberVar2;

    public:
      static return_type class_method();

      return_type instance_method1();
      return_type instance_method2( int p1 );
      return_type instance_method3( int p1, int p2 );
  }
  ```

- object-c 代码

  ```object-c
  @interface MyObject : NSObject {
    int memberVar1;
    id  memberVar2;
  }

  +(return_type) class_method;

  -(return_type) instance_method1;
  -(return_type) instance_method2: (int) p1;
  -(return_type) instance_method3: (int) p1 andPar: (int) p2;
  @end
  ```

## 类的实现

- object-c 代码

  ```object-c
  @implementation MyObject {
    int memberVar3;
  }

  +(return_type) class_method {
      ....
  }
  -(return_type) instance_method1 {
      ....
  }
  -(return_type) instance_method2: (int) p1 {
      ....
  }
  -(return_type) instance_method3: (int) p1 andPar: (int) p2 {
      ....
  }
  @end
  ```

## 对象初始化与构造函数

```object-c
- (id) init {
    if ( self=[super init] ) {
        // do something here ...
    }
    return self;
}

MyObject * my = [[MyObject alloc] init];

MyObject * my = [MyObject new];
```

## 协议

协议是一组没有实现的方法列表，类似于 Java 中的接口，可以用来做多重继承。

```object-c
@protocol Locking
- (void)lock;
- (void)unlock;
@end

@interface SomeClass : SomeSuperClass <Locking>
@end

@implementation SomeClass
- (void)lock {
  // do something
}
- (void)unlock {
  // do something
}
@end
```

## 动态类型

objectc 中的物件收到消息时，可以有三种处理措施。1.执行对应方法并回应消息。2.无法回应，可以将消息转发给别的物件。3.若以上两者都无法执行，抛出异常。

```object-c
- setMyValue:(id) foo;
// id 表示 foo 可以是任何类型的实例
- setMyValue:(id <aProtocol>) foo;
// id<aProtocol>表示 foo 可以是任何类型的实例，但必须采纳"aProtocal"协议
- setMyValue:(NSNumber*) foo;
// foo 必须是 NSNumber 的实例
```
