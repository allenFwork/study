# Shell基础

## 一、Shell 概述

### 1、概述

Shell 是一个命令行解释器，它为用户提供了一个向Linux内核发送请求以便运行程序的界面系统级程序，用户可以用Shell来启动、挂起·、停止甚至是编写一些程序。

Shell 还是一个功能相当强大的编程语言，易编写，易调试，灵活性较强。Shell是解释执行的脚本语言，在Shell中可以直接调用Linux系统命令。



### 2、Shell 的分类

- Bourne Shell ：从1979 起 Unix 就开始使用 Bourne Shell，Bourne Sheel 的主文件名为 sh 。
- C Shell ：C Shell 主要在 BSD 版的 Unix 系统中使用，其语法和 C 语言相类似而得名。



Shell 的两种主要语法类型有 Bourne 和 C ，这两种语法彼此不兼容。

- Bourne 家族主要包括：sh、ksh、Bash（Linux当中的标准Shell）、psh、zsh；
- C 家族主要包括：csh、tcsh



### 3、命令

**格式：[root@localhost ~]# echo $SHELL**

- 显示当前系统是执行的哪种shell



## 二、脚本执行方式

### 1、echo输出命令

**格式：<font color=red>echo [选项] [输出内容]</font>**

**选项：**

- -e ：支持反斜线控制的字符转换
- ![](images\echo输出字符选项.png)

**实例：**

1. [root@localhost ~]# echo -e "hell\b0"

- 删除左侧字符，结果显示 helo

2. [root@localhost ~]# echo -e "h\te\tl\tl\to"

- 制表符,相当于tab键，显示结果为 h	e	l	l	o

3. [root@localhost ~]# echo -e "hello\nword"

- 换行符

4. [root@localhost ~]# echo -e "\x68\t\x65\t\x6c\t\x6c\t\x6f"

- 按照十六进制ASCII码输出，显示结果为 h	e	l	l	o

5. **[root@localhost ~]# <font color=red>echo -e "\e[1;31m hello world \e[0m"</font>**

- **输出颜色：**
  - 30m=黑色，31m=红色，32m=绿色，33m=黄色，34m=蓝色，35m=洋红
  - 36m=青色，37m=白色
- **显示结果：**![](images\输出颜色设置.png)



### 2、写第一个脚本

[root@localhost sh]# vi hello.sh

```bash
#!/bin/bash
#The first program 注释行
echo -e "\e[1;34m hello world \e[0m"
```

- shell 脚本文件中只要开头使用了“#”，就表示此行内容不生效,是注释，除了“#!/bin/bash”例外



### 3、脚本执行

#### 3.1 赋予执行权限，直接运行

- chmod 755 hello.sh
- ./hello.sh

**注：必须通过相对路径或者绝对路径执行，不能直接写 hello.sh**

#### 3.2 通过 bash 调用执行脚本

- bash hello.sh

**注：不需要赋予执行权限给文件，也可以执行**



## 三、Bash的基本功能

### 1、命令别名与快捷键

#### 1.1 什么是别名？

- 命令别命就相当于人的小名

#### 1.2 查看与设定别名

**格式：alias**

- 查看系统中所有的命令别命

**格式：alias 别命='原命令'**

- 设定命令别名

**实例：**

![](images\查看别名和设置别名.png)

**注：通过上述方法设定的别命，只是临时生效，系统重启后会恢复原样**

#### 1.3 别命永久生效与删除别名

**格式：<font color=red>vi ~/.bashrc</font>，再写入需要添加的别名**

- 写入环境变量配置文件，从而实现永久生效
- 对超级用户来说，配置文件是 ~/.bashrc 文件；对普通用户来说，要找到对应的配置文件

**格式：unalias 别命**

- 删除别命，临时删除
- 如果想要永久删除别命，需要到 .bashrc 配置文件中删除对应的别命

#### 1.4 命令生效顺序

1. 第一顺位执行用绝对路径或相对路径执行的命令
2. 第二顺位执行别名
3. 第三顺位执行Bash的内部命令
4. 第四顺位执行按照 $PATH 环境变量定义的目录查找顺序找到的第一个命令

#### 1.5 常用快捷键

1. ctrl + c ：强制终止当前命令
2. ctrl + l  ：清屏
3. ctrl + a ：光标移动到命令行首
4. ctrl + e ：光标移动到命令行尾
5. ctrl + u ：从光标所在位置删除到行首
6. ctrl + z ：把命令放入后台
7. ctrl + r ：在历史命令中搜索



### 2、历史命令

#### 2.1 命令语法

**格式：history [选项] [历史命令保存文件]**

**选项：**

- -c  ：清空历史命令
- -w ：把缓存中的历史命令写入历史命令保存文件 ~/.bash_history

**注：历史命令默认会保存1000条，可以在环境变量配置文件 /etc/profile 中进行修改**

#### 2.2 历史命令的调用

- 使用上、下箭头调用以前的历史命令
- 使用 “!n” 重复执行第 n 条历史命令
- 使用 “!!” 重复执行上一条命令
- 使用 “!字串” 重复执行最后一条以该字串开头的命令

#### 2.3 命令与文件补全 

在 Bash 中，命令与文件补全是非常便利与常用的功能，我们只要在输入命令或文件时，按<font color=red> “Tab” 键</font>就会自动进行补全



### 3、输出重定向