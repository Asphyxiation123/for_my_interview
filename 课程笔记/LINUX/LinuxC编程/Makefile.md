# 1 makefile 三要素
![[Pasted image 20240517140719.png]]
目标、依赖、命令
三者关系：
```Makefile
目标:依赖的文件或者其他目标
<tab>命令1
	 命令2
	 ...
```
## gcc 命令集
```shell
g++ [options] source_files -o output_file
```
C 语言程序编译使用 gcc 命令，该流程包括：
```shell
gcc –E（预处理） test.c（源文件） -o test.i（将结果生成的文件）
gcc -S(汇编) test.i -o test.s（汇编编语言文件）
gcc -c（编译） test.s -o test.o
```
在功能上，预处理、编译、汇编是3个不同的阶段  
但gcc在实际操作时可以把这3个步骤合并为一个步骤来执行,即使用 -c选项：
```shell
gcc –c test.c -o test.o
```

```shell
#链接
gcc （没有选项符号） test.o example.o -o tes
```

>可用选项：  
-L 指定库路径  
-l （小写的L）指定库名称  
-I （大写的i）指定头文件所在路径  
-i 指定头文件名称
## Makefile 快速了解


# 2 基本语法
### 变量
分为系统变量、自定义变量、自动化变量
#### 赋值种类
=,延迟赋值，当使用到该变量是才会赋值
:=立即赋值
?=空赋值
+=追加赋值
#### 自动化变量
$< 代表第一个依赖文件
$^ 代表所有的依赖文件
$@ 表示目标
例：
```shell
all:targeta targetb
	echo "$<"	(targeta)
	echo "$^"	(targeta targetb)
	echo "$@"	(all)
```

`%.o` 通配符

`.PHONY` 虚构个目标，保证命令始终能执行，不会因同名文件干扰