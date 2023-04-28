# CMake-Learning
# CMake说明

cmake的定义是什么？-----高级编译配置工具；

当多个人用不同的语言或者编译器开发一个项目，最终要输出一个可执行文件或者共享库(dll，so等等)，这时候神器就出现了-----CMake！

所有操作都是通过**编译CMakeLists.txt**来完成的；

学习CMake的目的：为将来处理大型的C/C++/JAVA项目做准备；

# CMake安装

1、绝大多数的linux系统已经安装了CMake；

2、Windows或某些没有安装过的linux系统，去[官方网站](http://www.cmake.org/HTML/Download.html)可以下载安装；

# CMake应用

以Hello CMake为例：

1、步骤一，写源码；

```c++
#include <iostream>

int main(){
std::cout <<  "hello CMake" << std::endl;
}
```

2、步骤二，写CMakeLists.txt

```cmake
PROJECT (HELLO)

SET(SRC_LIST hello.cpp)

MESSAGE(STATUS "This is BINARY dir " ${HELLO_BINARY_DIR})

MESSAGE(STATUS "This is SOURCE dir "${HELLO_SOURCE_DIR})

ADD_EXECUTABLE(hello ${SRC_LIST})
```

3、步骤三，使用CMake，生成makefile文件

```shell
cmake ./	# 在当前目录下cmake
```

目录下就生成了这些文件：CMakeFiles, CMakeCache.txt, cmake_install.cmake等文件，并且生成了Makefile。
现在不需要理会这些文件的作用，以后你也可以不去理会。最关键的是，它自动生成了Makefile。

4、使用make命令编译

```shell
make
```

5、最终生成了Hello的可执行程序；

下面对介绍一些重要的关键字的含义：

## PROJECT关键字

可以用来指定工程的名字和支持的语言，默认支持所有语言

`PROJECT (HELLO)`-指定了工程的名字，并且支持所有语言(建议写法)

`PROJECT (HELLO CXX)`-指定了工程的名字，并且支持语言是C++

`PROJECT (HELLO C CXX)`-指定了工程的名字，并且支持语言是C和C++

该指定隐式定义了两个CMAKE的变量：

- `<projectname>_BINARY_DIR`，本例中是 HELLO_BINARY_DIR；

- `<projectname>_SOURCE_DIR`，本例中是 HELLO_SOURCE_DIR；

MESSAGE关键字就可以直接使用者两个变量，当前都指向当前的工作目录，后面会讲外部编译；

这样使用会产生的一个问题是：

- 如果改了工程名，这两个变量名也会改变

- 方案：又定义两个预定义变量：PROJECT_BINARY_DIR和PROJECT_SOURCE_DIR，这两个变量和HELLO_BINARY_DIR，HELLO_SOURCE_DIR是一致的。

## SET关键字

用来显示的指定变量：

`SET(SRC_LIST hello.cpp)`-SRC_LIST变量就包含了hello.cpp

也可以`SET(SRC_LIST main.cpp t1.cpp t2.cpp)`-即关联多个源文件；

## MESSAGE关键字

向终端输出用户自定义的信息。

主要包含三种信息：

- `SEND_ERROR`-产生错误，生成过程则会被跳过；
- `SATUS`-输出前缀为--的信息；
- `FATAL_ERROR`-则立即终止所有cmake过程；

## ADD_EXECUTABLE关键字

生成可执行文件

`ADD_EXECUTABLE(hello ${SRC_LIST})`-生成的可执行文件名是hello，源文件读取变量`SRC_LIST`中的内容；

也可以直接写`ADD_EXECUTABLE(hello hello.cpp)`；

上述例子可以简化的写成：

```cmake
PROJECT (HELLO)
ADD_EXECUTABLE (hello hello.cpp)
```

**注意：** 工程名的HELLO和生成的可执行文件hello是没有任何关系的；

# 语法的基本原则

- 变量使用`${}`方式取值，但是在IF控制语句中是直接使用变量名；
- `指令(参数1 参数2 ...)`参数使用括弧括起，参数之间使用空格或分号分开。 
  - 以上面的ADD_EXECUTABLE指令为例，如果存在另外一个func.cpp源文件，就要写成：
    
    - `ADD_EXECUTABLE(hello main.cpp func.cpp)`或者`ADD_EXECUTABLE(hello main.cpp;func.cpp)`；
    
- 指令是大小写无关的，参数和变量是大小写相关的，**但推荐你全部使用大写指令**；

## 语法注意事项

- `SET(SRC_LIST main.cpp)`可以写成`SET(SRC_LIST main.cpp)`，如果**源文件名中含有空格，就必须要加双引号**；
- `ADD_EXECUTABLE(hello main)`后缀可以不写，他会自动去找.c和.cpp文件，**最好不要这样写**，可能会有这两个文件main.cpp和main；

# 内部构建和外部构建

- 上述例子就是内部构建，他生产的临时文件都在当前目录下，很不方便清理；
- 外部构建，就会把生成的临时文件放在特定目录(build)下，不会对源文件有任何影响，强烈使用外部构建方式；

## 外部构建

1、建立一个build目录，可以在任何地方，建议在当前目录下；

2、进入build，运行`cmake ..`-".."表示上一级目录，也可以写CMakeLists.txt所在的绝对路径，生产的文件都在build目录下了；

3、在build目录下，运行make来构建工程；

注意外部构建的两个变量

1、`HELLO_SOURCE_DIR`-还是原工程路径；

2、`HELLO_BINARY_DIR`-编译路径，也就是build的绝对路径；

### 外部构建Hello CMake

让Hello CMake看起来更像一个工程，步骤如下：

- 为工程添加一个子目录src，用来放置工程源代码；
- 添加一个子目录doc，用来放置这个工程的文档，文档命名为hello.txt；
- 在工程目录添加两份md文件COPYRIGHT,README；
- 在工程目录添加一个`runhello.sh`脚本，用来调用hello二进制；
- 将构建后的目标文件放入构建目录(build)的bin子目录；
- 将doc目录的内容以及COPYRIGHT README安装到指定文件夹：`/usr/share/doc/cmake/`；

### 将目标文件放入构建目录的bin子目录

工程目录以及源码目录下都要有一个CMakeLists.txt说明；

外层CMakeLists.txt指定工程名，指定源码目录与构建目录；

```cmake
PROJECT (HELLO)
ADD_SUBDIRECTORY (src bin)
```

src下的CMakeLists.txt指定可执行文件的生成：

```cmake
ADD_EXECUTABLE (hello main.cpp)
```

### ADD_SUBDIRECTORY 指令

`ADD_SUBDIRECTORY (source_dir [binary_dir] [EXCLUDE_FROM_ALL])`

- 这个指令用于向当前工程添加存放源文件的子目录(**以工程目录作为根目录**)，并可以指定中间二进制和目标二进制存放的位置(**以执行该指令的目录作为根目录**)；
- `EXCLUDE_FROM_ALL`函数是将写的目录从编译中排除，如程序中的example；
- `ADD_SUBDIRECTORY(src bin)`：
  
    - 将src子目录加入工程并指定编译输出(包含编译中间结果)路径为bin目录；
    
    - 如果不进行bin目录的指定，那么编译结果(包括中间结果)都将存放在**build/src目录**；

### 更改二进制的保存路径

SET指令可重新定义EXECUTABLE_OUTPUT_PATH和LIBRARY_OUTPUT_PATH变量，来指定最终的目标二进制的位置：

```cmake
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)	# 即build/bin
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
```

**思考：** 加载哪个CMakeLists.txt当中？

哪里要改变目标存放路径，就在哪里加入上述的定义，所以应该在src下的CMakeLists.txt下写

# 安装工程

make下主要有两种方式：

- 一种是从代码编译后直接make install安装
- 一种是打包时的指定目录安装。
    - 简单的可以这样指定目录：`make install DESTDIR=/tmp/test`
    - 稍微复杂一点可以这样指定目录：`./configure –prefix=/usr`

## 安装HelloWord

使用CMake一个新的指令：`INSTALL`；

`INSTALL`的安装可以包括：二进制、动态库、静态库以及文件、目录、脚本等；

使用CMake一个新的变量：`CMAKE_INSTALL_PREFIX`；

### 安装文件COPYRIGHT和README

`INSTALL(FILES COPYRIGHT README DESTINATION share/doc/cmake/)`

FILES：文件，包括后续两份文件；

DESTINATION：

1、写绝对路径；

2、可以写相对路径，相对路径实际路径是：`${CMAKE_INSTALL_PREFIX}/<DESTINATION 定义的路径>`；

***Notes：*** `CMAKE_INSTALL_PREFIX`默认是在`/usr/local/`；

`cmake -DCMAKE_INSTALL_PREFIX=/usr`-在cmake的时候指定`CMAKE_INSTALL_PREFIX`变量的路径；

### 安装脚本run.sh

`PROGRAMS`：非目标文件的可执行程序安装(比如脚本之类)；

`INSTALL(PROGRAMS run.sh DESTINATION bin)`；

**说明：** 实际安装到的是`/usr/local/bin`；

### 安装doc中的hello.txt

两种方式：

- 通过在doc目录建立CMakeLists.txt，通过install下的file；
- 直接在工程目录通过`INSTALL (DIRECTORY doc/ DESTINATION share/doc/cmake)`；
  


DIRECTORY后面连接的是所在Source目录的相对路径；

**注意：** abc和abc/有很大的区别：

- 目录名不以/结尾：这个目录将被安装为目标路径下；

- 目录名以/结尾：这个目录中的内容安装到目标路径下；

### 安装过程

```shell
cmake ..    # ..为工程目录
make        # 读取该目录下的MakeFile文件
make install
```



# 静态库和动态库的构建

任务：

- 建立一个静态库和动态库，提供HelloFunc函数供其他程序编程使用，HelloFunc向终端输出Hello CMake字符串。 

- 安装头文件与共享库。

静态库和动态库的区别：

- 静态库的扩展名一般为".a"或".lib"；动态库的扩展名一般为".so"或".dll"。
- 静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可独立运行。
- 动态库在编译时不会放到连接的目标程序中，即可执行文件无法单独运行。

## 构建实例

写一个头文件hello.h：

```c++
// 函数声明
#ifndef HELLO_H
#define Hello_H

void HelloFunc();

#endif
```

hello.cpp中的内容

```c++
// 函数定义
#include "hello.h"
#include <iostream>
void HelloFunc(){
    std::cout << "Hello World" << std::endl;
}
```

项目中的cmake内容：

```cmake
PROJECT (HELLO)
ADD_SUBDIRECTORY (lib bin)
```

lib中CMakeLists.txt中的内容

```cmake
SET (LIBHELLO_SRC hello.cpp)
ADD_LIBRARY (hello SHARED ${LIBHELLO_SRC})
```

### ADD_LIBRARY

`ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})`

- hello：就是正常的库名，生成的名字前面会加上lib，最终产生的文件是`libhello.so`；
- `SHARED`：动态库；`STATIC`：静态库。
- `${LIBHELLO_SRC}` ：源文件

### 同时构建静态和动态库

```cmake
# 如果用这种方式，只会构建一个动态库，不会构建出静态库，虽然静态库的后缀是.a
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello STATIC ${LIBHELLO_SRC})

# 修改静态库的名字，这样是可以的，但是我们往往希望他们的名字是相同的，只是后缀不同而已
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})
```

此时可以通过`SET_TARGET_PROPERTIES`这条指令可以用来设置输出的名称，对于动态库，还可以用来指定动态库版本和API版本；

同时构建静态和动态库：

```cmake
SET(LIBHELLO_SRC hello.cpp)
ADD_LIBRARY(hello_static STATIC ${LIBHELLO_SRC})

# 对hello_static的重名为hello
SET_TARGET_PROPERTIES(hello_static PROPERTIES  OUTPUT_NAME "hello")

ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
```

上述指令实现了同时构建，同时关注到`SET_TARGET_PROPERTIES`关键字的作用：

```cmake
# cmake 在构建一个新的target时，会尝试清理掉其他使用这个名字的库，因为，在构建libhello.so时，就会清理掉libhello.a
SET_TARGET_PROPERTIES(hello_static PROPERTIES CLEAN_DIRECT_OUTPUT 1)
SET_TARGET_PROPERTIES(hello PROPERTIES CLEAN_DIRECT_OUTPUT 1)
```

### 动态库的版本号

一般动态库都有一个版本号的关联

```shell
libhello.so.1.2		# 动态库的实际文件
libhello.so ->libhello.so.1	# 链接API版本为1的文件
libhello.so.1->libhello.so.1.2	# API链接的实际文件
```

CMakeLists.txt 插入如下

`SET_TARGET_PROPERTIES(hello PROPERTIES VERSION 1.2 SOVERSION 1)`

VERSION指代动态库版本，SOVERSION指代API版本。

### 安装共享库和头文件

本例中我们将hello的共享库安装到`<prefix>/lib`目录，

将hello.h安装到`<prefix>/include/hello` 目录

```cmake
# 文件放到该目录下
INSTALL(FILES hello.h DESTINATION include/hello)

# 二进制，静态库，动态库安装都用TARGETS
# ARCHIVE特指静态库，LIBRARY特指动态库，RUNTIME特指可执行目标二进制。
INSTALL(TARGETS hello hello_static LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
```

**注意：** 安装的时候，指定一下路径，放到系统下，这样`#include`就能直接包含我们的hello.h：

`cmake -DCMAKE_INSTALL_PREFIX=/usr ..`

### 使用外部共享库和头文件

准备工作，新建一个目录来使用外部共享库和头文件：

写一份main.cpp

```cpp
#include <hello.h>

int main(){
	HelloFunc();
}
```

***Notes：*** 该源文件的CMake编译按照之前的流程走即可，不赘述；

**可能会遇到的两个问题：**

**1. make后头文件找不到的问题**

PS：`include <hello/hello.h>`这样include是可以，这么做的话，就没啥好讲的了

关键字：INCLUDE_DIRECTORIES    这条指令可以用来向工程添加多个特定的头文件搜索路径，路径之间用空格分割

在CMakeLists.txt中加入头文件搜索路径

`INCLUDE_DIRECTORIES(/usr/include/hello)`

**2. 找不到引用的函数问题**

报错信息：undefined reference to HelloFunc()，CMake下的解决方案解决方案：

- 关键字：`LINK_DIRECTORIES`-先添加非标准的共享库搜索路径；
  - 指定第三方库所在路径：`LINK_DIRECTORIES(.../myproject/libs)`

- 关键字：`TARGET_LINK_LIBRARIES`-再添加具体需要链接的共享库；
  - `TARGET_LINK_LIBRARIES`只需要给出链接库的名字就行了：`TARGET_LINK_LIBRARIES(main libhello.a)`

在CMakeLists.txt中插入链接共享库，主要要插在executable的后面；

### 特殊的环境变量

`CMAKE_INCLUDE_PATH`和`CMAKE_LIBRARY_PATH`；

注意：这两个是环境变量而不是cmake变量，可以在linux的bash中进行设置；

我们上面例子中使用了绝对路径`INCLUDE_DIRECTORIES(/usr/include/hello)`来指明include路径的位置；

我们还可以使用另外一种方式，使用环境变量`export CMAKE_INCLUDE_PATH=/usr/include/hello`；

补充：生产debug版本的方法：`cmake .. -DCMAKE_BUILD_TYPE=debug`(<font color=red>这里还不是很了解，留坑</font>)
