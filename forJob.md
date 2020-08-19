# Hello 这是小葛的面试笔记本

## 基础知识

### 说一下C++和C的区别

+ 设计思想上：  
  C++是面向对象的语言，而C是面向过程的结构化语言；
+ 语法上  
  C++具有重载，继承和多态三种特性  
  C++相比C，增加了许多类型安全的功能  
  C++支持范式编程，比如模板类，函数模板等

### 说一下C++中static关键字的作用

对于函数定义和代码块之外的变量声明，static修改标识符的链接属性，  
由默认的external转变为internal，作用域和存储类型不变，这些符号只能在声明它们的源文件中访问。  
对于被static修饰的普通函数，其只能在定义它的源文件中使用，不能在其他源文件中被引用。  
对于被static修饰的类成员变量和成员函数，它们是属于类的，而不是某个对象，所有对象共享一个静态成员。静态成员通过<类名>::<静态成员>来使用。

### static的作用

+ 全局静态变量
  在全局变量前加上关键字static，全局变量就定义成一个全局静态变量。  
  静态存储区，在程序运行期间一直存在。  
+ 局部静态变量
  在局部变量之前加上关键字static，局部变量就成为一个局部静态变量。  
  内存中的位置：静态存储区。  
+ 静态函数
  在函数返回类型前加static，函数就定义为静态函数。函数的定义和声明在默认情况下都是extren的，但是静态函数只是在声明他的文件当中可用，不能被其他文件所用。  
  函数的实现使用static修饰，那么函数只能在cpp中使用，不会与其他cpp中同名函数冲突。  
  不要在头文件中声明static的全局函数。
+ 类的静态成员
  在类中，静态成员可以实现多个对象之间的数据共享，并且使用静态数据成员还不会破坏隐藏的原则，既保证了安全性。  
+ 类的静态函数  
  静态成员函数和静态数据成员一样，它们都属类的静态成员，它们都不是对象成员。因此，对静态成员的引用不需要用对象名。  
  调用静态成员函数使用如下格式：<类名>::<静态成员函数名>(<参数表>)。  

### C++中四种cast转换  

C++中四种类型转换是：static_cast，dynamic_cast，const_cast，reinterpret_cast。  

+ const_cast
  用于将const变量转为非const
+ static_cast
  用于各种隐式转换，比如非const转为const，void*转指针等，static_cast能用于多态向上转化，如果向下转能成功但是不安全。  
+ dynamic_cast
  用于动态类型转换。只能用于含有虚函数的类，用于类层次的向上和向下转换，只能转指针或引用。向下转化时，如果是非法的指针返回NULL。  
  向上转换：子类向基类的转换。  
  向下转换：基类向子类的转换。
+ reinterpret_cast
  什么都可以转换，比如int转指针，但是可能出问题。  
+ 为什么不用C的强制转换？
  转换不够明确，不易检查。

### 指针和引用的区别  

+ 指针友自己的一块空间，而引用只是一个别名。  
+ 使用sizeof查询指针的大小微商4，而引用则是被引用对象的大小。  
+ 指针可以被初始化为NULL，而引用必须被初始化且必须是一个已有对象的引用。  
+ 作为参数传递时，指针需要被解引用才可以对对象进行操作，而直接对引用的修改都会改变引用所指向的对象。  
+ 可以有const指针，但是没有const引用。  
+ 指针在使用过程中可以指向其他对象，但是引用只能是一个对象的引用。  
+ 指针可以有多级指针，而引用只有一级。  
+ 指针和引用使用++运算符的含义不一样。  
+ 如果返回动态内存分配的对象或者内存，必须使用指针，引用可能会引发内存泄漏。  
  
### 给定三角形ABC和一点P(x, y, z)，判断P点是否在ABC内

```C++
#define ABS_FLOAT_0 0.0001

struct point_float{
    float x;
    float y;
};

//计算三角形面积
float GetTraingleSquar(const point_float pt0, const point_float pt1, const point_float pt2){
    point_float AB, BC;
    AB.x = pt1.x-pt0.x;
    AB.y = pt1.y-pt0.y;
    BC.x = pt2.x-pt1.x;
    BC.y = pt2.y-pt1.y;

    return fabs(AB.x*BC.y - AB.y*BC.x) / 2.0f;
}

//判定给定一点是否在三角形内或者边上
bool InIntraingle(const point_float A, const point_float B, const point_float C, const point_float D){
    float SABC, SAPB, SAPC, SBPC;
    SABC = GetTraingleSquar(A, B, C);
    SAPB = GetTraingleSquar(A, P, B);
    SAPC = GetTraingleSquar(A, P, C);
    SBPC = GetTraingleSquar(B, P, C);

    float SumSquar = SAPB + SAPC + SBPC;

    if((SABC - SumSquar) > -ABS_FLOAT_0 && (SABC - SumSquar) < ABS_FLOAT_0>){
        return true;
    }
    else return false;
}
```

### 野指针

野指针就是指向一个已删除的对象或者未申请访问受限内存区域的指针。  

### 智能指针的内存泄露如何解决

为了解决循环引用导致的内存泄露，引用了weak_ptr弱指针，weak_ptr的构造函数不会修改引用计数的值，从而不会对的对象的内存进行管理。类似一个普通指针，但不指向引用计数的共享内存，但是可以检测到所管理的对象是否已经被释放，避免非法访问。  

### 为什么析构函数必须是虚函数？C++默认的析构函数不是虚函数  

将可能会被继承的父类的析构函数设置为虚函数，可以保证当我们new一个子类，然后使用基类指针指向该子类的对象，释放基类指针时可以释放掉子类的空间，防止内存的泄露。  
