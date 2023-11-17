### 编译过程

![image-20231107151143468](./images/image-20231107151143468.png)

GCC 将C/C++文件编译成可执行文件需要四步。“gcc -o hello.exe hello.c”执行如下：

1.预处理：通过 GNU C 预处理器 (cpp.exe)，它包含头文件 (#include) 并扩展宏 (#define)

2.编译：编译器将预处理过的源代码编译成特定处理器的汇编代码

3.汇编： 汇编程序（as.exe）将汇编代码转换为对象文件 "hello.o "中的机器代码

4.链接器： 最后，链接器（ld.exe）将目标代码与库代码链接起来，生成可执行文件 "hello.exe"

> 预处理：`gcc -E introduce.c -o introduce.i`

> 编 译：`gcc -S introduce.i -o introduce.s`

> 汇 编：`gcc -c introduce.s -o introduce.o`

> 链 接：`gcc  introduce.o -o introduce`

### 头文件搜索顺序

1. 先搜索当前目录
2. 然后搜索`-I`指定的目录
3. 再搜索gcc的环境变量CPLUS_INCLUDE_PATH（C程序使用的是C_INCLUDE_PATH）
4. 最后搜索gcc的内定目录
   - /usr/include
   - /usr/local/include
   - /usr/lib/gcc/x86_64-redhat-Linux/4.1.1/include

### 库文件搜索顺序

**编译的时候：**

1. gcc会去找-L
2. 再找gcc的环境变量LIBRARY_PATH
3. 再找内定目录 /lib /usr/lib /usr/local/lib

**运行时动态库的搜索路径：**

1. 编译目标代码时指定的动态库搜索路径（这是通过gcc 的参数"-Wl,-rpath,“指定。当指定多个动态库搜索路径时，路径之间用冒号”：“分隔）

2. 环境变量LD_LIBRARY_PATH指定的动态库搜索路径（当通过该环境变量指定多个动态库搜索路径时，路径之间用冒号”："分隔）

3. 配置文件/etc/ld.so.conf中指定的动态库搜索路径；

4. 默认的动态库搜索路径/lib；

5. 默认的动态库搜索路径/usr/lib。

   > 应注意动态库搜寻路径并不包括当前文件夹，所以当即使可执行文件和其所需的so文件在同一文件夹，也会出现找不到so的问题，类同#include <header_file>不搜索当前目录

### include路径添加

除了默认的/usr/include, /usr/local/include等include路径外，还可以通过设置环境变量来添加系统include的路径：
**C**

```shell
export C_INCLUDE_PATH=XXXX:$C_INCLUDE_PATH
```

**CPP**

```shell
export CPLUS_INCLUDE_PATH=XXX:$CPLUS_INCLUDE_PATH 
```

以上修改可以直接命令行输入（一次性），可以在/etc/profile中完成（对所有用户生效），也可以在用户home目录下的.bashrc或.bash_profile中添加（针对某个用户生效），修改完后重新登录即生效。

### Link链接库文件路径

链接库文件在连接（静态库和共享库）和运行（仅限于使用共享库的程序）时被使用，其搜索路径是在系统中进行设置的（也可以在编译命令中通过 -l -L 来指定，这里讲的是使用系统默认搜索路径）。
一般 Linux 系统把 /lib /usr/lib /usr/local/lib 作为默认的库搜索路径，所以使用这几个目录中的链接库文件可直接被搜索到（不需要专门指定链接库路径）。对于默认搜索路径之外的库，则需要将其所在路径添加到gcc/g++的搜索路径之中。
链接库文件的搜索路径指定有两种方式：1）修改/etc/so.ld.conf 2）修改环境变量，在其中添加自己的路径

1) 修改环境变量
动态链接库搜索路径：
export LD_LIBRARY_PATH=XXX:$LD_LIBRARY_PATH

静态链接库搜索路径：
export LIBRARY_PATH=XXX:$LIBRARY_PATH

以上修改可以直接命令行输入（一次性），可以在/etc/profile中完成（对所有用户生效），也可以在用户home目录下的.bashrc或.bash_profile中添加（针对某个用户生效）,修改完后重新登录即生效。

2) 修改/etc/so.ld.conf
在/etc/ld.so.conf 中添加指定的链接库搜索路径（需要root权限），然后运行 /sbin/ldconfig，以达到刷新 /etc/ld.so.cache的效果,这一步很重要。

LD_LIBRARY_PATH和LIBRARY_PATH区别，LD_LIBRARY_PATH用于运行时动态库的查询路径，而LIBRARY_PATH是用于编译时的查找路径。一个是运行时，一个是编译时。



### 头文件(.h)，静态库（static libraries）（.lib,.a） Shared Library (.dll, .so)



外部库有两种类型：静态库和共享库。

静态库：把所有得依赖都打包成一个库文件

共享库：共享库的文件扩展名在 Unix 中为".so"（shared objects），在 Windows 中为".dll"（dynamic link library）。程序与共享库链接时，只在可执行文件中创建一个小表。在可执行文件开始运行之前，操作系统会加载外部功能所需的机器代码，这一过程称为动态链接。动态链接使可执行文件更小，节省磁盘空间，因为多个程序可以共享一份库。此外，大多数操作系统允许所有运行程序使用内存中的一份共享库，从而节省了内存。共享库代码可以升级，无需重新编译程序

### 搜索头文件和库（-I、-L 和-l）

编译程序时，编译器需要头文件来编译源代码；链接器需要库来解析来自其他对象文件或库的外部引用

-I(大写i):

-L:

-l:

**Default Include-paths, Library-paths and Libraries**

Try list the default include-paths in your system used by the "GNU C Preprocessor" via "`cpp -v`":

```shell
> cpp -v
......
#include "..." search starts here:
#include <...> search starts here:
 /usr/lib/gcc/x86_64-pc-cygwin/6.4.0/include
 /usr/include
 /usr/lib/gcc/x86_64-pc-cygwin/6.4.0/../../../../lib/../include/w32api
```

Try running the compilation in verbose mode (`-v`) to study the library-paths (`-L`) and libraries (`-l`) used in your system:

```
> gcc -v -o hello.exe hello.c
......
-L/usr/lib/gcc/x86_64-pc-cygwin/6.4.0
-L/usr/x86_64-pc-cygwin/lib
-L/usr/lib
-L/lib
-lgcc_s     // libgcc_s.a
-lgcc       // libgcc.a
-lcygwin    // libcygwin.a
-ladvapi32  // libadvapi32.a
-lshell32   // libshell32.a
-luser32    // libuser32.a
-lkernel32  // libkernel32.a
```

### g++编译，使用静态，动态库

`g++` 命令可以用于编译静态库和动态库，具体取决于你提供的源代码和编译选项。

编译静态库（`.a` 文件）的示例：

```shell
g++ -c -o mylib1.o mylib1.cpp  # 编译源文件为目标文件
g++ -c -o mylib2.o mylib2.cpp
ar rcs libmylib.a mylib1.o mylib2.o  # 创建静态库
```

上述示例中，`mylib1.cpp` 和 `mylib2.cpp` 是你的源代码文件，它们被编译成目标文件 `mylib1.o` 和 `mylib2.o`。然后，使用 `ar` 命令创建静态库 `libmylib.a`。

编译动态库（`.so` 文件）的示例：

```shell
g++ -shared -o libmylib.so mylib1.cpp mylib2.cpp  # 编译为动态库
```

上述示例中，`mylib1.cpp` 和 `mylib2.cpp` 直接被编译成动态库 `libmylib.so`。使用 `-shared` 选项告诉编译器生成一个共享库。



#### 使用静态库

1. **编译时链接静态库**：
   - C/C++: 在编译命令中指定静态库的路径和名称，例如：`gcc -o myapp myapp.c -L/path/to/library -lmylib`.
   - 链接器将在可执行文件中包含静态库的代码，使得可执行文件不依赖外部库文件。
2. **在源代码中包含头文件**：
   - 在你的源代码中包含静态库的头文件，以便可以调用库中的函数和使用库的数据结构。

#### 使用动态库

1. **运行时加载动态库**：
   - C/C++: 使用 `dlopen` 函数加载动态库，然后使用 `dlsym` 获取库中的函数指针，最后使用这些函数。
   - 具体操作依赖于编程语言和平台。
2. **在源代码中包含头文件**：
   - 与静态库一样，需要在源代码中包含动态库的头文件以便使用库中的功能。
3. **设置库路径**：
   - 在运行时，需要告诉操作系统动态库的位置。可以通过设置 `LD_LIBRARY_PATH` 环境变量或将库路径添加到 `/etc/ld.so.conf` 配置文件中。
4. **编译和链接时指定动态库**：
   - 在编译和链接时指定动态库的名称和路径，以确保程序能够找到库文件。

### cmake编译静态动态库



- 生成静态库

```cmake
cmake_minimum_required(VERSION 3.19)

project(CMAKE)

set(CMAKE_CXX_STANDARD 11)
# 指定源文件
set(SRC_FILES Box.cpp funcation.cpp Log.cpp student.cpp)

# 指定头文件目录
include_directories(./include)

# 创建静态库
add_library(test STATIC ${SRC_FILES})

```

- 生成动态库

```cmake
cmake_minimum_required(VERSION 3.19)

project(CMAKE)

set(CMAKE_CXX_STANDARD 11)
# 指定源文件
set(SRC_FILES Box.cpp funcation.cpp Log.cpp student.cpp)

# 指定头文件目录
include_directories(./include)

# 创建静态库
add_library(test SHARED ${SRC_FILES})

```



### 其它

- 当你使用`ldd`来查看一个动态库的依赖时，你可以看到它依赖的所有共享库

```shell
[root@comtom ~]# ldd libmp3lame.so.0 
	linux-vdso.so.1 =>  (0x00007ffd69ddb000)
	libm.so.6 => /lib64/libm.so.6 (0x00007f46ccccc000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f46cc8fe000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f46cd25d000)

```

- 尝试使用`readelf`工具来分析动态库的符号表，以查看其静态库依赖关系

```shell
readelf -Ws /path/to/your/dynamic/library.so
```

这将列出动态库的符号表信息，包括其依赖的符号和库。虽然这不会直接列出静态库的依赖，但你可以查看符号表以确定是否有静态库的引用。

