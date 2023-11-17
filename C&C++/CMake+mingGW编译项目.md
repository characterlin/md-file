## 配置MingGW编译器

### 下载

https://github.com/niXman/mingw-builds-binaries

### 配置环境变量

D:\mingw64\bin

检查环境

```
gcc --version
```

## 安装Cmake

下载
cmake 官网https://cmake.org/download/

配置环境变量

D:\cmake-3.26.5-windows-x86_64\bin

检查环境

``` shell
cmake -version
```

## vsode+cmake+mingGW构建项目

![image-20231108113834117](./images/image-20231108113834117.png)

### 编写一个简单程序

main.cpp

```c++
#include <iostream>

int main()
{
    std::cout << "hello world" << std::endl;
    return 0;
}
```

CMake编译流程

1. 编写CMakeLists.txt
2. 执行 cmake PATH 生成Makefile（注：PATH 为 CMakeLists.txt 所在目录）
3. 执行make编译

### 编写 CMakeLists.txt

```CMake
add_executable(main main.cpp)
```

说明

- add_executable：使用指定的源文件来生成目标可执行文件

### 生成 Makefile 文件

```CMake
cmake .
```

### 编译项目

```CMake
make
```

### 修复告警

```CMake
cmake_minimum_required(VERSION 3.14)

project(hello)

add_executable(main main.cpp)
```

- cmake_minimum_required：用于设定 CMake 的最低版本
- project：指定工程名称

### 语言版本

有的时候代码中用到了 c++ 新版的特性，像 C++11，C++14，C++17 等等。

```CMake
# 增加 -std=c++11
set(CMAKE_CXX_STANDARD 11)

# 增加 -std=c++14
set(CMAKE_CXX_STANDARD 14)

# 增加 -std=c++17
set(CMAKE_CXX_STANDARD 17)
```

### 编译选项

CMakeLists.txt

```C++
add_compile_options(-g -Wunused)
```

说明

- add_compile_options：给后续的目标加上编译选项

CMakeLists.txt

```C++
target_compile_options(main PUBLIC -Wall -Werror)
```

说明

- target_compile_options：给指定的目标加上编译选项

## 多文件编译

### 单个添加

add.h

```C++
#pragma once

int add(int x, int y);
```

add.cpp

```C++
#include "add.h"

int add(int x, int y)
{
    return x + y;
}
```

main.cpp

```C++
#include <iostream>
#include "add.h"

int main()
{
    int res = add(1, 2);
    std::cout << "1 + 2 = " << res << std::endl;
    return 0;
}
```

CMakeLists.txt

```C++
cmake_minimum_required(VERSION 3.14)

project(hello)

add_executable(main add.cpp main.cpp)
```

sub.h

```C++
#pragma once

int sub(int x, int y);
```

sub.cpp

```C++
#include "sub.h"

int sub(int x, int y)
{
    return x - y;
}
```

main.cpp

```C++
#include <iostream>
#include "add.h"
#include "sub.h"

int main()
{
    int res = add(1, 2);
    std::cout << "1 + 2 = " << res << std::endl;

    res = sub(9, 1);
    std::cout << "9 - 1 = " << res << std::endl;
    return 0;
}
```

CMakeLists.txt

```C++
cmake_minimum_required(VERSION 3.14)

project(hello)

add_executable(main add.cpp sub.cpp main.cpp)
```

### 自动添加

自动把 src 目录下的 cpp 文件添加到项目里

CMakeLists.txt

```C++
cmake_minimum_required(VERSION 3.14)

project(hello)

file(GLOB_RECURSE SOURCES "src/*.cpp")

add_executable(main ${SOURCES} main.cpp)
```

说明

file：获取 src 目录下的所有.cpp 文件，并将其保存到变量中

问题：

如果源文件分布在多个目录下怎么办？

### 包含头文件

CMakeLists.txt

```C++
cmake_minimum_required(VERSION 3.14)

project(hello)

include_directories(./)

file(GLOB_RECURSE SOURCES "src/*.cpp")

add_executable(main ${SOURCES} main.cpp)
```

说明

- include_directories：添加头文件搜索路径

问题：

如何添加多个头文件搜索路径？

## 多目标编译

test.cpp

```C++
#include <iostream>
#include "add.h"

int main()
{
    int res = add(2, 3);
    std::cout << "2 + 3 = " << res << std::endl;
    return 0;
}
```

CMakeLists.txt

```C++
cmake_minimum_required(VERSION 3.14)
project(hello)
set(CMAKE_CXX_STANDARD 11)

include_directories(./)

file(GLOB_RECURSE SOURCES "src/*.cpp")

add_executable(main ${SOURCES} main.cpp)
add_executable(test ${SOURCES} test.cpp)
```

编译将会生成两个可执行文件：main、test

## 静态库编译 & 链接

### 编译

mul/mul.h

```C++
#pragma once

int mul(int x, int y);
```

mul/mul.cpp

```C++
#include "mul.h"

int mul(int x, int y)
{
    return x * y;
}
```

CMakeLists.txt

```C++
set(MUL_SOURCES ./mul/mul.cpp)
add_library(mul STATIC ${MUL_SOURCES})
```

说明

- add_library：指定从某些源文件创建库文件（静态库、动态库）

### 链接

main.cpp

```C++
#include <iostream>
#include "add.h"
#include "sub.h"
#include "mul.h"

int main()
{
    int res = add(1, 2);
    std::cout << "1 + 2 = " << res << std::endl;

    res = sub(9, 1);
    std::cout << "9 - 1 = " << res << std::endl;

    res = mul(3, 5);
    std::cout << "3 * 5 = " << res << std::endl;
    return 0;
}
```

CMakeLists.txt

```C++
link_directories(./)
link_libraries(mul)
```

或者

```C++
target_link_directories(main PUBLIC ./)
target_link_libraries(main mul)
```

## 动态库编译 & 链接

### 编译

CMakeLists.txt

```C++
set(MUL_SOURCES ./mul/mul.cpp)
add_library(mul SHARED ${MUL_SOURCES})
```

说明

- add_library：指定从某些源文件创建库文件（静态库、动态库）

> 注意：如果是编译器用的是msvc(微软的visual studio) 编译器，导出动态库时，会同时导出静态库和动态库，需要注意在编译的时候需要连接静态库，但是运行时需要依赖动态库 这和使用MingGW编译器不同点



### 链接

CMakeLists.txt

```C++
link_directories(./)
link_libraries(mul)
```

或者

```C++
target_link_directories(main PUBLIC ./)
target_link_libraries(main mul)
```



## CMake+MingGW编译项目存在问题

- 报错

```shell
D:\test_argtable3\build>cmake ..
-- Building for: NMake Makefiles
CMake Error at CMakeLists.txt:5 (project):
  Running

   'nmake' '-?'

  failed with:
CMake Error: CMAKE_C_COMPILER not set, after EnableLanguage
CMake Error: CMAKE_CXX_COMPILER not set, after EnableLanguage
-- Configuring incomplete, errors occurred!
See also "D:/test_argtable3/build/CMakeFiles/CMakeOutput.log".
   系统找不到指定的文件。
```

问题是因为cmake默认使用windows的nmake程序（本机没有所以提示找不到nmake），因此需要指明cmake要生成ming_make使用的makefile文件：cmake -G"MinGW Makefiles" …



- **The C compiler "C:/MinGW/bin/gcc.exe" is not able to compile a simple test program.**

​      使用 CMake + MinGW 时的一个问题--它们不支持非拉丁符号。保证项目路径不含非拉丁字符



## CMakeLists.txt常用命令

```cm

include_directories(dir1 dir2...)
#添加额外的头文件目录 相当于gcc的 -Idir1 dir2...
#如果除了系统默认的头文件目录之外，没有程序额外需要的头文件目录，可以不写
 
link_directories(dir1 dir2...)
#添加额外的库文件目录 相当于gcc的 -Ldir1 dir2...
#如果除了系统默认的库文件目录之外，没有程序额外需要的库文件目录，可以不写
 
link_libraries(lib1 lib2...)
#设置所有目标需要链接的库 相当于gcc的 -llib1 lib2...
#如果除了标准库之外，没有其他非标准库，可以不写，反之link_libraries或target_link_libraries二选一
#注意这个命令必须用在add_executable()命令之前
 
target_link_libraries(targetname lib1 lib2...)
#设置单个目标需要链接的库 相当于gcc的 -llib1 lib2...
#如果除了标准库之外，没有其他非标准库，可以不写，反之link_libraries或target_link_libraries二选一
#注意这个命令必须用在add_executable()命令之后
 
add_executable(abcd src/abcd.c)
#将abcd.c编译生成可执行文件abcd
 
add_library(abcd STATIC src/abcd.cpp)
#添加自己编写的库文件，#STATIC表示生成静态库abcd.a文件，SHARED表示生成静态库abcd.so文件
```

CMakeLists.txt模板

```cmake
## 最低CMake版本要求
cmake_minimum_required(VERSION 3.5.1)
 
#使用通配符添加多个源文件名列表赋给SRC_LIST
file(GLOB SRC_LIST "src/*.c")
#将src工作目录的绝对路径赋给SRC_DIR
file(GLOB SRC_DIR "src")
#将src的前一级目录所在的文件夹名赋给project_name 也就是工程文件夹名作为工程名
#.表示匹配任意一个字符 *表示其左边的字符被匹配0次或多次  .*结合起来就是匹配任意多个字符
#()表示保存匹配的表达式并随后替换它  \\1表示之前匹配到的第1个()内的内容
string(REGEX REPLACE ".*/(.*)/src" "\\1" project_name ${SRC_DIR})
# 项目名称
project(${project_name})
#添加debug信息，使得生成的可执行文件可以调试
#set(CMAKE_BUILD_TYPE DEBUG)
#编译选项
add_compile_options(-std=c++11)
# 头文件路径
include_directories("include")
#链接库
#link_libraries(event)
#为每一个源文件生成一个可执行文件 可执行文件名即为.c前缀之前的源文件名
foreach(src ${SRC_LIST})
    string(REGEX REPLACE ".*/(.*).c" "\\1" program ${src})
    add_executable(${program} ${src})
endforeach(src)
#将所有源文件生成一个可执行文件
#add_executable(${project_name}  ${SRC_LIST})
```

### 推荐文章

http://lihuaxi.xjx100.cn/news/96114.html?action=onClick
