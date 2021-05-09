#! https://zhuanlan.zhihu.com/p/370778146
new是C++新引入的操作符（相对C)，为什么要引入new呢？  

考虑在自由存储区创建一个对象，有两个步骤：
1. 在自由存储区中分配存储
2. 调用构造函数初始化对象  

你是希望写：
```cpp
X *p = new X(2);
```
还是写：
```cpp
struct X *p = (struct X *) malloc(sizeof(struct X));
if (p==0) error("memory exhausted");
p->init(2);
```
显然，引进new运算符，更简单也更不容易出错。  

但也有一个问题，用于实现new的C语言自由空间分配函数malloc变成了执行瓶颈。一个发现是主导自由存储区使用的，通常只是很少的几个类，如果我们不用默认的malloc分配存储，而是为类定制分配存储的函数，这样可以提高效率。（这一块的理解是比较含糊的，作者也说得比较含糊，没给例子。。。）  

为了能够覆盖默认的存储分配方式，C++后续又引入了operator new（上面讲的那个叫new operator，不是同一个东西)。  
```cpp
class X {
  // ...
public:
  void* operator new(size_t sz);     // allocate sz bytes
  void operator delete(void* p);     // free p

  X();          // initialize
  X(int i);     // initialize

  ~X();         // cleanup
  // ...
}
```
现在再调用`new X(2)`的时候，就会先调用`X::operator new()`分配存储，再调用X的构造函数初始化。  

参考：  
《C++语言的设计和演化》2.1，3.9，10.2