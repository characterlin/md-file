## 基本语句
### 数字

```python
>>> 5
5
>>> 5+9
14
```

### 文本

使用single quotes 或者 double quotes

```python
>>> "hhh"
'hhh'
>>> 'hhh'
'hhh'
```

字符串定义和输出可能不同，主要是转义符\的原因，可能被转义成特殊字符，例如：

```
>>> 'hello\n'
'hello\n'
>>> s ='hello\n'
>>> s
'hello\n'
>>> print(s)
hello
```

```
print('C:\some\name')  # here \n means newline!


print(r'C:\some\name')  # note the r before the quote
```

字符串字面值可以包含多行。 一种实现方式是使用三重引号：`"""..."""` 或 `'''...'''`。 字符串中将自动包括行结束符，但也可以在换行的地方添加一个 `\` 来避免此情况。

```python
print("""\
Usage: thingy [OPTIONS]
     -h                        Display this usage message
     -H hostname               Hostname to connect to
""")
```



### 列表

与 [immutable](https://docs.python.org/zh-cn/3.12/glossary.html#term-immutable) 字符串不同, 列表是 [mutable](https://docs.python.org/zh-cn/3.12/glossary.html#term-mutable) 类型，其内容可以改变

```python
string = 'today is wonderful!'
string = "da"

#string[0]='a' //error
print(string[0:8])


# 列表
cubes = [0,1,3212,31231]
cubes[3] = 9999 //yes
print(cubes)
cubes=cubes+[1,3]
print(cubes)
```

### 循环中的break,continue语句及else子句

[`break`](https://docs.python.org/zh-cn/3.12/reference/simple_stmts.html#break) 语句将跳出最近的一层 [`for`](https://docs.python.org/zh-cn/3.12/reference/compound_stmts.html#for) 或 [`while`](https://docs.python.org/zh-cn/3.12/reference/compound_stmts.html#while) 循环。

**`for` 或 `while` 循环可以包括 `else` 子句。**

在 [`for`](https://docs.python.org/zh-cn/3.12/reference/compound_stmts.html#for) 循环中，`else` 子句会在循环成功结束最后一次迭代之后执行。

在 [`while`](https://docs.python.org/zh-cn/3.12/reference/compound_stmts.html#while) 循环中，它会在循环条件变为假值后执行。

无论哪种循环，如果因为 [`break`](https://docs.python.org/zh-cn/3.12/reference/simple_stmts.html#break) 而结束，那么 `else` 子句就 **不会** 执行。

```python
for n in range(2, 10):
    for x in range(2,n):
        if(n % x == 0):
            print(n, "=", x, "*", n//x)
            break
    else:
            print(n, "is a prime number!")
```

上面代码中 else子句属于for循环，不属于if语句。

else子句用于循环时比起if语句的else子句，更像try语句。**try语句的else子句再未发生异常时执行，循环的else子句则在未发生break时执行**

### pass语句
pass语句不执行任何动作，语法上需要一个语句，但程序不需执行任何动作，可以使用该语句
```python
while True:
    pass
```
可以创建最小的类
```python
class Test:
    pass
```
还可用作函数或条件语句体的占位符，让你保持在更抽象的层次进行思考。pass 会被默默地忽略
```python
def initlog(*args):
    pass   # Remember to implement this!
```


### match语句
接受一个表达式并把它的值与一个或多个 case 块给出的一系列模式进行比较。这表面上像 C、Java 或 JavaScript（以及许多其他程序设计语言）中的 switch 语句，但其实它更像 Rust 或 Haskell 中的模式匹配。只有第一个匹配的模式会被执行，并且它还可以提取值的组成部分（序列的元素或对象的属性）赋给变量。

```python
def http_error(status):
    match status:
        case 400:
            return "Bad request"
        case 404:
            return "Not found"
        case 418:
            return "I'm a teapot"
        case _:
            return "Something's wrong with the internet"
```
注意最后一个代码块：“变量名” _ 被作为 通配符 并必定会匹配成功。如果没有 case 匹配成功，则不会执行任何分支。

你可以用 | （“或”）将多个字面值组合到一个模式中：

### 区分直接执行还是模块导入
在 Python 中，解释器是通过运行时的上下文（context）来确定 __name__ 变量的值的。具体来说：

当 Python 脚本直接运行时，解释器将设置 __name__ 为 "__main__"。

当 Python 模块被导入时，__name__ 将是模块的名称。

这是 Python 解释器的默认行为。因此，Python 不会事先知道你的代码中是否包含 if __name__ == "__main__": 这个条件判断语句，而是在运行时根据实际情况确定 __name__ 的值。

这种机制使得你能够在脚本中使用 if __name__ == "__main__": 来区分脚本是被直接执行还是被导入为模块使用。如果你确实有 if __name__ == "__main__": 条件，而且脚本被直接执行，相应的代码块就会被执行。如果没有这个条件，或者脚本是被导入为模块使用的，那么相应的代码块就不会被执行。


## 定义函数



## 错误和异常
错误至少被分为两种， *语法错误*和*异常*
### 语法错误
语法错误又称解析错误，是学习 Python 时最常见的错误：
```python
while True print('Hello world')
  File "<stdin>", line 1
    while True print('Hello world')
                   ^
SyntaxError: invalid syntax
```
### 异常
执行时检测到的错误称为*异常*
```python
10*(1/0)  //报ZERODivisionError  0不能做除数
4+xx*3    //报NameError   xx未定义
'2'+2     //TypeError     str只能和str 连接 concatenate
```
### 异常的处理
下面会要求用户一直输入内容，直到输入有效的整数
``` python
while True:
    try:
        x=int(input("please input a number"))
        break
    except ValueError:
        print("Oops! That was no valid number. Try Again!")
```
try语句的工作原理：
- 先执行 try子句 
- 如果没有触发异常，则跳过 except 子句，try 语句执行完毕。
- 如果在执行 try 子句时发生了异常，则跳过该子句中剩下的部分。 如果异常的类型与 except 关键字后指定的异常相匹配，则会执行 except 子句，然后跳到 try/except 代码块之后继续执行。
- 如果发生的异常与 except 子句 中指定的异常不匹配，则它会被传递到外层的 try 语句中；如果没有找到处理句柄，则它是一个 未处理异常 且执行将停止并输出一条错误消息。

