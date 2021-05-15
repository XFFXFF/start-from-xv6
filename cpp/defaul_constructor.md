如果没有显式定义任何constructor，编译器就会隐式地生成default constructor。编译器帮我们生成的default constructor会做哪些事情呢？  
```cpp
class Foo {
public:
  int val;
  Foo *pnext;
}
```
考虑上面这段代码，编译器生成的default constructor会帮我们初始化val和pnext吗？会初始化val为0，初始化pnext为nullptr吗？  
不会   

```cpp
class Bar { public: Bar() ... };
class Foo {
public:
  Bar bar;
  int val;
  Foo *pnext;
}
```
考虑上面这段代码，编译器为Foo生成的default constructor会做哪些事情呢？  
会调用class Bar的default constructor来处理member object Foo::bar，但并不会产生任何代码来初始化Foo::val和Foo::pnext。  
伪代码长这样
```cpp
inline Foo::Foo() {
  bar.Bar::Bar();
}
```

如果我们显式地定义default constructor，编译器就不会再管default constructor生成了吗？  
比如
```cpp
Foo::Foo() {
  val = 0;
  pnext = nullptr;
}
```
这个时候，编译器会扩充default constructor，扩充为
```cpp
// C++伪代码
Foo::Foo() {
  bar.Bar::Bar();
  val = 0;
  pnext = nullptr;
}
```
总的来说，我感觉编译器如果知道member object该怎么初始化，就会在default constructor中帮我们初始化，如果它不知道，也就没法帮我们初始化。member object自身有default construtor，编译器就知道它该怎么初始化，比如Foo::bar；如果没有，那编译器就没法帮我们初始化，比如Foo::val和Foo::pnext，它不能不负责任的随便猜一个值。  

参考：  
《深度探索C++对象模型》2.1