## chapter2 开始学习c++

### c++程序的基本结构

#### 预编译指令

指编译之前的预处理

##### 宏定义

宏定义与常量区别在于

- 宏定义是直接替换，不会分配内存，存储与程序的代码段中；
- const常量需要进行内存分配，存储与程序的数据段中

##### 文件包含

\#include <> 包含一个系统([编译器](https://so.csdn.net/so/search?q=编译器&spm=1001.2101.3001.7020)自带)的头文件

\#include " " 包含的是一个用户定义的文件,可以是[头文件](https://so.csdn.net/so/search?q=头文件&spm=1001.2101.3001.7020),也可是普通文件

##### 条件编译

条件满足才编译

有时某个头文件会被不同的文件引用，为了防止某个头文件被多次引用

该头文件(stdio.h 添加下滑线)使用以下语句

```c++
#ifndef _STDIO_H_ 
#define _STDIO_H_  //if not define 才执行，防止define以后语句被重复定义
int a = 10;
#endif 
```



#### 名称空间

可作为附加信息来区分不同库中相同名称的函数、类、变量

默认std空间，所以无需通过 命名:标识符访问



## chapter3  类与对象

### 构造函数

**缘由**：我们定义一个类，类中的private变量需要初始化，但对于该类对应的不同对象，我们希望初始化不同的值

但默认生成的构造函数不支持主函数定义时直接赋初值，因此我们需要自己定义一个构造函数

```c++
class Point{
private:
  int x,y;
public:
  //构造函数，定义类时被调用，不能定义返回值类型，不能有return，可以内联，可以重载
  Point(int xx=0, int yy=0) {x=xx; y=yy;} //默认参数值，只有当没有被赋值时才起作用
  
  Point(const Point& p);//复制构造函数
  void setX(int xx) {x=xx;}
  void setY(int yy) {y=yy;}
  int getX() const{ return x;} //常函数
  int getY() const{ return y;}
};
```

### 复制构造函数

**缘由**：用一个已经存在的对象初始化新的对象，需要复制

自动生成的复制构造函数实现对应成员复制

自定义的复制构造函数可以实现特殊的复制功能

```c++
//复制构造函数
Point::Point(const Point& p){
  x = p.x;
  y = p.y;
  cout << "Calling the copy constructor"<<endl;
}
```

**调用：**复制对象时    函数形参是类的对象时

```C++
void fun1(Point p){
  cout <<p.getX()<<endl;
}

Point fun2(){
  Point a(1,2);
  return a;
}

int main(){
  Point a(4,5);
  Point b(a);  //用a初始化b,此时会调用复制构造函数
  cout << b.getX() << endl;
  fun1(b);//形参是对象p,此时也会调用复制构造函数
  b = fun2();//返回对象时也会调用，有的编译器不显示
  cout << b.getX() << endl;
  return 0;
}void fun1(Point p){
  cout <<p.getX()<<endl;
}

Point fun2(){
  Point a(1,2);
  return a;
}

int main(){
  Point a(4,5);
  Point b(a);  //用a初始化b
  cout << b.getX() << endl;
  fun1(b);
  b = fun2();
  cout << b.getX() << endl;
  return 0;
}
```

### 左值引用和右值引用

引用指同一个变量，同时使用两个名字

左值引用指对持久存在变量的引用

右值引用指对短暂存在可被移动变量的引用

```c++
float n = 6; //n为左值，6为右值
float &lr_n = n; //左值引用
float &&rr_n = n; //错误的右值引用，因为n为左值
float &&rr_n = n*n; //正确的右值引用，右值表达式可以用作右值引用
//可使用move函数将n转化为右值

```

### 类的组合

我们希望用小的类组成大的类，需要关注类组合时的构造函数

```c++
class Line{
public:
  Line(Point xp1,Point xp2); //对比 Point(int xx=0, int yy=0) {x=xx; y=yy;} 
  Line(Line &l); //没有const也可
  double getLen() {return len;}
private:
  Point p1,p2; //类组合
  double len;
}; //不要忘记分号
```

此时，构造函数的形参为类

#### 类组合时的构造函数

以下两种写法都可以完成构造

第一种是使用初始化列表

```C++
Line::Line(Point xp1,Point xp2):p1(xp1),p2(xp2)
{
    cout << "Calling constructor of Line" << endl;
}
```

第二种直接赋值

```c++
//组合类的构造函数
Line::Line(Point xp1,Point xp2)
{
    p1 = xp1;
    p2 = xp2;
    cout << "Calling constructor of Line" << endl;
}
```

经实验，两种方法都会两遍调用point类的**复制构造函数**，为xp1 xp2传参

但第一种方法会额外调用两次point类的**复制构造函数**，为p1 p2赋值

原因未知

#### 类的嵌套

看下列代码

```c++
class B; //前向引用声明
class A{
public:
  void f(B b);
};
class B{
public:
  void g(A a);
};
```

A中需要B，B中需要A，需要先声明B，这样class A才可以通过编译

再看下面例子，会报错

```c++
class Fred;
class Barney{
  Fred x;
};
class Fred{
  Barney y;
};
```

这是因为 `Fred x;`和 `void f(B b);`不同

后者**将未详细定义的类作为形参**，**只是完成声明，此时编译器并没有分配内存**

当具体定义函数时，才会分配内存

而前者的使用条件时Fred类型完全已知



## chapter 4 数据的共享与保护

### 数据的作用域

枚举类型的作用域 ——留个坑

### 静态数据成员

对于静态局部变量，用static定义，

- **如果在某个函数被声明，那么此变量只会在该函数被第一次调用时初始化**
- **可以用于类之间共享数据**
- **默认初始化为0**

但存在一个问题，如果在类中定义静态变量，访问该变量不方便，如果没有定义类的话

### 静态函数成员

