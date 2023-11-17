编译cpp文件，报**undefined reference to `std::cout‘**

- gcc和g++都是GNU(组织)的一个编译器。


- 后缀名为.c的程序和.cpp的程序g++都会当成是c++的源程序来处理。而gcc不然，gcc会把.c的程序处理成c程序。

- 对于.cpp的程序，编译可以用gcc/g++，而链接可以用g++或者gcc -lstdc++。

代码示例：

```c++
#include <iostream>
int main()
{
    int const x = 10;
    std::cout << x  << std::endl;
    return 0;
}
```

上面使用`gcc`编译`test.cpp`会报错

1. 我们常见的编译器有两个：
   1. `gcc` 编译器
   2. `g++` 编译器
   3. `gcc`和`g++`都是GNU(组织)的编译器。
2. gcc和g++编译器的区别

   1. `g++`： 会把.c和.cpp的文件都当作是C++的源程序进行编译。

   2. `gcc`：会把.c的程序当作是C的源程序进行编译，`.cpp`的程序当作是C++的源程序进行编译

解决上面的错误，就是把gcc编译器换成g++编译器，即使是把`.cpp`的后缀改成.c的后缀也可以正常编译！！！



gcc和g++的主要区别

1. 对于 *.c和*.cpp文件，gcc分别当做c和cpp文件编译（c和cpp的语法强度是不一样的）
2. 对于 *.c和*.cpp文件，g++则统一当做cpp文件编译
3. 使用g++编译文件时，**g++会自动链接标准库STL，而gcc不会自动链接STL**
4. gcc在编译C文件时，可使用的预定义宏是比较少的

5. gcc在编译cpp文件时/g++在编译c文件和cpp文件时（这时候gcc和g++调用的都是cpp文件的编译器），会加入一些额外的宏，这些宏如下：

```c
#define __GXX_WEAK__ 1
#define __cplusplus 1
#define __DEPRECATED 1
#define __GNUG__ 4
#define __EXCEPTIONS 1
#define __private_extern__ extern
```

6. 在用gcc编译c++文件时，为了能够使用STL，需要加参数 –lstdc++ ，但这并不代表 gcc –lstdc++ 和 g++等价，它们的区别不仅仅是这个
