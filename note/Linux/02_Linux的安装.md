# Linux的安装

## 1、VMware简介

VMware是一个虚拟PC的软件，可以在现有的操作系统上虚拟出一个新的硬件环境，

相当于模拟出一台新的PC，以此来实现在一台机器上实现真正同时运行两个独立的操作系统



## 2、虚拟机的使用

### 2.1 新建虚拟机

- 初学使用CentOS 32位系统来学习
- 虚拟机不能够删除干净，需要使用VMware_Install_Cleaner来删除

### 2.2 虚拟机硬件设置

- 内存：
- CPU： 

### 2.3 快照

### 2.4 管理 - 克隆



## 3、系统分区

### 3.1 磁盘分区

- 主分区：最多只能有4个

- 扩展分区：

  1）最多只能有1个

  2）主分区加扩展分区最多有4个

  3）不能写入数据，只能包含逻辑分区

### 3.2 格式化

格式化（高级格式化）又称逻辑格式化，它是指根据用户选定的文件系统（如FAT16、FAT32、NTFS、EXT2、EXT3、EXT4等），在磁盘的特定区域写入特定数据，在分区中划出一片用于存放文件分配表、目录表等用于文件管理的磁盘空间。

- FAT16：用于管理2G的磁盘，不适用了（windows中）
- FAT32：用于管理16T的磁盘大小，但是单个文件最大为4G（windows中）
- NTFS：（windows目前使用的）
- ETX2：（Linux中）
- ETX3：
- ETX4：CentOS中默认使用

格式化，将分区后的磁盘进行换分为等大小的数据块（block），建立二维表格存储相关信息

格式化的目的是为了写入文件系统

![](images\磁盘分区.png)

### 3.3 硬件设备文件名

![](images\硬件设备文件名.png)

- “/” 代表<font color=red> 根目录</font>，根是最高一级的目录
- Linux是树形结构
- dev目录是专门用来放硬件设备的



### 3.4 分区设备文件名

#### 3.4.1 设备文件名

- /dev/hda1（IDE硬盘接口）
- /dev/sda1（SCSI硬盘接口、SATA硬盘接口）

注：sda1： sd表示SATA硬盘，a表示第一块硬盘，1表示第一个分区

#### 3.4.2 分区表示

![](images\Linux分区命名.png)

- 第一个分区命名为 sda1234
- 第二个分区命名为 sdb12
- 扩展分区中的逻辑分区开始必须是5，不能从2开始

#### 3.4.3 挂载

1. 必须分区

- / （根分区）
- swap分区（）

2. 推荐分区

- /boot（启动分区，200MB）

![](images\挂载点.png)

- 目录成为挂载点，也就相当于windows下的盘符

#### 3.4.4 总结

- 分区：将大硬盘分为小的逻辑分区
- 格式化：写入系统文件
- 分区设备文件名：给每个分区定义设备文件名
- 挂在：给每个分区分配挂载点



## 4、 Linux系统安装

打开虚拟机 --> 鼠标点击虚拟机 --> 快速按 F2 键进入BIOS界面  --> 修改Boot项，通过按<font color=red>“+”</font>改变启动顺序，将CD-ROM Drive改为第一启动顺序

- BIOS界面是 主板的BIOS界面

- Boot 就是启动项

  1）Hard Drive 硬盘启动

  2）CD-ROM Drive 光驱启动

- CD-ROM Drive修改为第一启动顺序，表示通过光驱启动

- Exit 就是退出

  1）Exit Saving Changes : 保存选项退出

  2）Exit Discarding Changes : 

- 第一次安装时，使用光驱启动，将其改为了第一启动顺序，但是第二次启动时，还要进入Bios界面，将硬盘启动改为第一启动顺序，否则就是没启动一次都重新安装。

- <font color=red>Ctrl + Alt + Enter ：三件联合可以将虚拟机全屏</font>

### 4.1 安装欢迎界面

- Install or upgrade an existing system ：安装或升级现有系统

- Install system with basic video driver ：安装过程采用基本的显卡驱动

- Rescue installed system ：进入系统修复模式

- Boot from local drive ： 退出安装从硬盘启动

- Memory test ：存储介质检测

### 4.2 密码原则

- 复杂性

  1）八位字符以上、大小写字母、数字、符号

  2）不能是英文单词

  3）不能是和用户相关内容

- 易记忆性
- 时效性

### 4.3 软件包选择

- Desktop 桌面
- Minimal Desktop 最小化桌面
- Minimal 最小化
- Basic Server 基本服务器
- Database Server 数据库服务器
- Web Server 网页服务器
- Virtual Host 虚拟主机
- software development workstation 软件开发工作站

### 4.4 安装日志

- /root/install.log：存储了安装在系统中的软件包及其版本信息、
- /root/install.log.syslog：存储了安装过程中留下的事件记录
- /root/anaconda-ks.cfg：以Kickstart配置文件的格式记录安装过程中设置的选项信息



注:

 CentOS 6.x的默认文件系统类型是 EXT4

