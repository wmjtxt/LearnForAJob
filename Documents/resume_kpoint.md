
* [MySQL]()
* [Oracle]()
* [poi]()
* [epoll]()
* [redis]()
* [log4cpp]()
* [socket]()
* [C/S模式]()
* [C++, Java, Python]()
* [STL]()
* [Linux环境编程]()
* [GDB](#gdb)
* [Make](#make)
* [多线程编程]()
* [git,github]()


# GDB
* 1.编译时加上-g
```
$ g++ *.cpp -g
```
* 2.执行时
```
$ gdb a.out
```
或者分两步
```
$ gdb
(gdb)file a.out
```
* 命令

|命令|含义|对应windows|
|-|-|-|
|l|显示代码||
|r|开始运行|F9|
|b 4|在第四行设置断点|F5|
|info b|查看所有断点||
|delete [断点号]|查看所有断点||
|n|下一步,不进入函数|F10|
|s|表示单步进入函数|F11|
|p i|打印变量i||
|c|继续运行,直到函数结束或遇到新的断点||
|q|退出||


# Make

格式：

target: dependency_files

<tab>command

.PHONY是Makefile文件的关键字，表示它后面列表中的目标均为伪目标。伪目标通常用在清理文件、强制重新编译等情况下

自定义文件名的话,比如makename执行时在后面加上-f makename
```
make -f makename
```

**变量函数与规则**
随着项目越来越大，源文件也越来越复杂，这个时候就要用到变量，写出通用性较强的makefile文件
## 定义变量的两种方法
* 1.变量名=变量值   递归变量展开（几个变量共享一个值） //不常用
* 2.变量名:=变量值  简单变量展开（类似于C++的赋值）    //常用

变量分为; 用户自定义变量，预定义变量（CFLAGS），自动变量，环境变量

自动变量：指在使用的时候，自动用特定的值替换，常用的有: 

|变量|说明|
|$@|当前规则的目标文件（重点）|
|$<|当前规则的第一个依赖文件|
|$^|当前规则的所有依赖文件，以空格分隔（重点）|
|$?|规则中日期新于目标文件的所有相关文件列表，逗号分隔|
|$(@D)|目标文件的目录名部分|
|$(@F)|目标文件的文件名部分|

预定义变量：内部事先定义好的变量，但是它的值是固定的，并且有些的值是为空的。

AR: 库文件打包程序默认为ar
AS: 汇编程序，默认为as
CC: c编译器默认为cc
CPP: C预编译器，默认为$(CC) -E
CXX: C++编译器，默认为g++
RM: 删除，默认为rm -f
ARFLAGS: 库选项，无默认
ASFLAGS: 汇编选项，无默认
CFLAGS: c编译器选项，无默认
CPPFLAGS: c预编译器选项，无默认
CXXFLAGS: c++编译器选项

## 函数

* 1.wildcard : 搜索当前目录下的文件名，展开成一列所有符合由其参数描述的文件名，文件间以空格间隔。
`SOURCES=$(wildcard *.cpp)`把当前目录下所有.cpp文件存入变量SOURCES里。

* 2.字符串替换函数: $(patsubst要查找的子串，替换后的目标子串，源字符串)。将源字符串(以空格分隔)中的
所有要查找的子串替换成目标子串。如OBJS=$(patsubst %.cpp,%.o,$(SOURCES))
把SOURCES中的.cpp替换为.o

* 3.$(addprefix 前缀，源字符串)函数把第二个参数列表的每一项前缀上第一个参数值???


下面是一个较为通用的makefile:
```makefile
DIR      := ./debug
EXE      := $(DIR)/main.exe
CC       := g++
LIBS     :=
SRCS     := $(wildcard *.cpp) $(wildcard *.c) $(wildcard *.cc)
OCPP     := $(patsubst %.cpp, $(DIR)/%.o, $(wildcard *.cpp))
OC       := $(patsubst %.c, $(DIR)/%.co, $(wildcard *.c))
OCC      := $(patsubst %.cc, $(DIR)/%.cco, $(wildcard *.cc))
OCC      := $(OC) $(OCC) $(OCPP)
RM       := rm -rf
CXXFLAGS := -Wall -g
start : mkdebug $(EXE)
mkdebug :
    @if[! -d $(DIR)]; then mkdir $(DIR); fi;
$(EXE) : $(OBJS)
    $(CC) -o $@ $(OBJS) $(addprefix -l, $(LIBS))
$(DIR)/%.o : %.cpp
    $(CC) -c $(CXXFLAGS) $< -o $@
$(DIR)/%.co : %.c
    $(CC) -c $(CXXFLAGS) $< -o $@
$(DIR)/%.cco : %.cc
    $(CC) -c $(CXXFLAGS) $< -o $@

.PHONY : clean rebuild
clean :
    @D$(RM) $(DIR)/*.exe $(DIR)/*.o $(DIR)/*.co $(DIR)/*.cco
rebuild : clean start


```

## make的命令行选项
|命令格式|含义|
|-C dir|读入指定目录下的makefile|
|-f file|读入当前目录下的file文件作为makefile|
|-i|忽略所有的命令行执行错误|
|-I dir|指定被包含的makefile所在目录|
|-n|只打印要执行的命令，但不执行这些命令|
|-p|显示make变量数据库和隐含规则|
|-s|在执行命令时不显示命令|
|-w|如果make在执行过程中改变目录, 则打印当前目录名|

## 采用for循环编译多个目标文件的Makefile写法

...
