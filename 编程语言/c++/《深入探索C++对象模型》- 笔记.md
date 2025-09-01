## 1. 关于对象
### 1.1 C++ 对象模式
### 1.2 关键词所带来的差异
### 1.3 对象的差异
三种 C++ 程序设计模型：

（1）程序模型

（2）抽象数据类型模型

（3）面向对象模型

只有**指针**或者**引用**才支持多态特性。

C++ 以下方法支持多态：

+ 经由一组隐含的转换操作
```C++
shape *ps = new circle();
```

对象的大小：
+ 非静态数据成员的总和大小
+ 由于对齐需要填补的大小
+ 支持 虚函数

#### 1.3.1 指针的类型

#### 1.3.2 加上多态之后

## 2. 构造函数语意学
### 2.1 默认构造函数的建构操作

有用的默认构造函数的四种情况：

#### 2.1.1 带有默认构造函数的成员类对象

一个类没有任何显示构造函数并且成员类对象拥有默认构造函，那么它的默认构造函数就是有用的。例如：
```C++
class Foo {};

class Bar {
public:
    Foo foo;
    char *str;
};
```
编译器将会为Bar生成一个默认构造函数，这个默认构造函数会调用foo的默认构造函数实现foo的初始化。但是不会初始化 ```str```。

#### 2.1.2 带有默认构造函数的基类

同样的，如果一个没有任何构造函数的类派生自一个带有默认构造函数的基类，那么这个派生类的默认构造函数就是有用的。

这个生成的默认构造函数会调用基类的默认构造函数（根据它们的声明次序）。

#### 2.1.3 带有一个虚函数的类

下面两种情况，也会生成默认构造函数：

1. class 声明（或继承）一个**虚函数**；
2. class 派生自一个继承串链，其中有一个或更多的**虚拟基类**
```C++
class Widget {
public:
    virtual void flip() = 0;
};

class Bell : public Widget {
public:
    virtual void flip() {}
};

class Whistle : public Widget {
public:
    virtual void flip() {}
};
```
编译期间两个操作：
1. 产生 vtbl
2. 产生 vptr

为了初始化 vptr, 需要生成默认构造函数，并初始化 vptr。

#### 2.1.4 带有一个虚基类的类

**虚基类** 的实现法在不同的编译器之间有极大的差异。但是有一个共同点：必须使**虚基类**在每一个**派生类**中的位置能够在执行期准备妥当。

```C++
class X { public: int i; };
class A : public virtual X { public: int j; };
class B : public virtual X { public: double d; };
class C : public A, public B { public: int k; };

void fool(const A* pa) { pa->i = 1024; }
```
```cfont```的实现通过引用或指针指向**虚基类**，对**虚基类**的操作通相关指针完成。为此需要一个默认构造函数。

### 2.2 拷贝构造函数的建构操作

用一个对象去初始化另一个对象，有三种情况：

（1）直接赋值
```C++
X xx = x;
```
（2）作为函数参数
```C++
foo(xx)
```
（3）作为函数返回值
```C++
foo_bar()
{
    ......
    return xx;
}
```
拷贝构造函数：
```C++
X::X(const X& x);
```
当用一个对象初始化另一个对象时，通常都会调用拷贝构造函数。

#### 2.2.1 默认成员初始化

如果一个类没有显示声明拷贝构造函数，

#### 2.2.2 位逐次拷贝

如果一个类的声明符合**默认复制语义**，那么它就不需要复制构造函数：
```C++
class Word {
private:
    int cnt;
    char *str;
};
```
#### 2.2.3 不要位逐次拷贝

不符合**默认复制语义**的四种情况：

1. 内部成员对象所在类声明了**复制构造函数**
2. 当继承的基类存在**复制构造函数**
3. 声明了一个或多个**虚拟函数**
4. 存在**虚拟基类**

#### 2.2.4 重新设定**虚函数表**的指针

因为存在```vptr```，拷贝构造函数需要考虑如何对```vptr```进行初始化。
```C++
class ZooAnimal {
public:
    virtual void draw();
};

class Bear : public ZooAnimal {
public:
    virtual void draw();
};
void foo()
{
    Bear yogi;
    Bear winnie = yogi;    // 安全

    ZooAnimal franny = yogi;    // 发生切割行为
    franny.draw();              // 调用ZooAnial::draw();
}
```

#### 2.2.5 处理虚拟类子对象

### 2.3 程序转化语意学

#### 2.3.1 明确的初始化操作

#### 2.3.2 参数的初始化

## 3. Data 语意学

```C++
class X {};
class Y : public virtual X {}
class Z : public virtual X {}
class A : public Y, public Z {}
```
+ ```sizeof(X)```的结果为 1
  X 并不是空的，它有一个隐晦的 1byte，那是被编译器安插进去的一个```char```，使得它的每个对象在内存中都有独一无二的地址；
+ ```sizeof(Z)```和```sizeof(Y)```的结果为 8
  (1) 对```虚基类的支持```，在对象内部需要一个指针指向表格
  (2)

### 3.1 Data Member 的绑定

### 3.2 Data Member 的布局

非静态数据成员在类对象中的排列顺序将和其被声明的顺序一样。

C++ 标准要求，在同一个 access section中，成员的排列只需符合“较晚出现的成员在类对象中较高的地址”这一条件即可（意味着不一定连续排列）。

C++ 标准 也允许编译器将多个 access sections 之中的 data members 自由排列，不必在乎它们出现在 class 声明中的次序。

```C++
template<class class_ype,
         class data_type1,
         class data_type2>
char* access_order(data_type1 class_type::*mem1, data_type2 class_type::*mem2)
{
    ......
}

access_order(&Point3d::z, &Point3d::y);
```

### 3.3 Data Member 的存取

```C++
Point3D origin, *pt = &origin;
// 存取数据成员
origin.x = 0.0;
pt-> x = 0.0;
```
差异？

### 3.3.1 静态数据成员

静态数据成员的存取以及与class的关联，并不会导致任何空间上或执行时间上的额外负担。

## 3.4 “继承” 与 Data Member

### 3.4.1 只要继承不要多态

### 3.4.2 加上多态

### 3.4.3 多重继承

### 3.4.4 虚拟继承

空间和存取时间的额外负担：

+ vtable + type_info(用以支持 RTTI)
+ 每个对象新增vptr
+ 加强 constructor，设定 vptr 初值；
+ 加强 destructor，

虚拟继承实现方法：一个类如果内含一个或多个虚基类对象，将被分割为两部分：一个不变局部和一个共享局部。

不变局部中的数据，不管后继如何衍化，总是拥有固定的 offset，所以从这一部分数据可以被直接存取。

共享局部，和虚拟基类对象有关，这一部分的数据，其位置会因为每次的派生操作而有变化，只能被简介存取。


## 3.5 对象成员的效率

## 3.6 指向 Data Members 的指针

去类成员的地址

```C++
& Point3d::z;
```
获取类成员 z 在class object中的偏移量。

```C++
float Point3d::*p1 = 0;
float Point3d::*p2 = &Point3d::x;
```
```Point3d::* ```指向 Point3d data member 的指针类型。

### 3.6.1 指向 Members 的指针的效率问题


## 4. Function 语意学

C++ 支持三种类型的**成员函数**: **静态成员函数**、**非静态成员函数**和**虚函数**，每一种类型被调用的方式都不同。

### 4.1 成员函数的各种调用方式

#### 4.1.1 非静态成员函数

C++11的设计准则之一就是：非静态成员函数至少和一般的非成员函数有相同的效率。











  

