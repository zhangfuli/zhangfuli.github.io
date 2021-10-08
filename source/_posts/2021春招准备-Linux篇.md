---
title: Linux篇-2021春招准备
date: 2021-02-27 15:45:40
tags:
---

#### 1、理解linux下的load

有多少进程在等待被CPU调度

Linux下，系统平均负载指的是运行队列平均长度，也就是等待cpu的平均进程数。

```shell
uptime
load = process/total_cpu_cores 
load average: 0.09, 0.05, 0.01
```

- 当车不多的时候，load <1；
- 当车占满整个马路的时候 load=1；
- 当马路都站满了，而且马路外还堆满了汽车的时候，load>1
- 两个cpu，load_average最大能到2

**负载的好和坏：**

需要根据机器当前的进程数与CPU总核数的比值（process_cores_values）和CPU总核数（total_cores）来比较

- 当 process_cores_values <= total_cores 时，机器负载在CPU合理承受范围内

- 当 process_cores_values > total_cores 时，机器负载超过CPU承受范围，机器超负荷运行。

在我们执行**top**或者**uptime**的时候会有3个load的值，那我们根据哪个值做为我们判断依据呢？

着眼5分钟和15分钟的load值比较合适，因为1分钟的值有可能是个瞬间值，而5分钟或者15分钟的值是平均值，长时间负载高，机器肯定是需要排查问题的。



##### top 字段：按f, 可以选择相关参数; 按d标示是否显示; 按q标示退出; t切换CPU

```shell
top - 15:47:18 up 86 days,  1:07,  2 users,  load average: 0.43, 0.38, 0.39

Tasks: 157 total,   1 running, 156 sleeping,   0 stopped,   0 zombie
(进程数: 运行中、睡眠、停止、僵尸进程)

%Cpu(s):  4.5 us,  6.3 sy,  0.0 ni, 88.4 id,  0.6 wa,  0.0 hi,  0.2 si,  0.0 st
%CPU：自从上一次更新时到现在任务所使用的CPU时间百分比。

KiB Mem :  8009184 total,   439224 free,  2005724 used,  5564236 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  5448920 avail Mem
%MEM：进程使用的可用物理内存百分比。
```

- PID：Process ID    PPID(Parent Process ID)
- USER
- PR: 优先级
- NI: Nice Value
- S: Process Status
  - D - 不可中断的睡眠态。
  - R – 运行态
  - S – 睡眠态
  - T – 被跟踪或已停止
  - Z – 僵尸态



##### 其他：

- 线程: `top -p PID -H`
- 监控Java线程数: `ps -eLf | grep java | wc -l`
- 监控网络客户连接数: `netstat -n | grep tcp | grep 侦听端口 | wc -l`



#### 2、常见命令

- 创建多级目录:  `makedir -p`

- 查看线程: `top -H`

- 命令用于计算字数: `wc`

- 查看文件夹大小: `ls -lh`

- 查看堆栈: `pstack PID` + `gdb`调试死锁

- 查看端口: `netstat -anop`

- 删除重复行: 

  - `sort -u XXX.txt`
  - `sort -k2n file | uniq > a.out`

- 文本流编辑: `sed`

- `vim`: 命令模式、插入模式、尾行命令模式
  
  - 命令模式下:
    - yy-复制
    - p-粘贴
    - 0-行首
    - 1G-文章首行 2G 3G
    - GG-文章末行
    - /查找的字符   n下一个;N上一个
  - 尾行命令模式
    - :%s/str1/str2/g 替换全局所有str1为str2
    - :s/str1/str2 
    - :n,s/str1/str2某行的str1替换为str2
    - :wq
  
- tcpdump: linux抓包工具

  - ```shell
    tcpdump -i eth1 src host 192.168.1.1  # 源地址
    tcpdump -i eth1 dst host 192.168.1.1  # 目标
    tcpdump -i eth1 src/dst port 25  # 端口
    tcpdump -i eth1 arp # 协议过滤
    tcpdump -i eth1 net 192.168 # 网络过滤
    ```




#### 3、服务器响应变慢排查

- 第一步: 查看系统资源是否达到上限
  - 查看一下CPU占比比较高的进程
  - 使用jstack命令生成进程的堆栈信息
  - 若频繁发生FULL GC，需要看一下内存快照，分析一下内存情况（可以使用java自带的或第三方工具）
    - jmap -dump:live,format=b,file=m.hprof PID
    - MAT分析工具
  - 如果是磁盘空间满了，及时清理磁盘
  - 如果是带宽满了，联系网络工程师解决
- 第二步: 
  - 检查应用服务器（Jboss/Tomcat）的线程池配置是否合理
  - 看一下请求的排队现象是否严重
  - 如果严重则需要重新设置合理的线程池
  - 检查一下数据库的连接池设置是否合理，增大连接池设置
  - 检查一下是否有慢sql，如果有慢sql，则进行优化（优化方案是查看执行计划，设置合理的索引等）

- 第三步：查看访问慢的服务的调用链
  - 查看一下调用链中的每一步响应时间是否合理
  - 如果不合理，则联系相关系统的负责人进行排查和解决
- 第四步：检查web服务器的请求日志
  - 看一下是否存在Doss攻击
    - 分布式拒绝服务, 用肉鸡对目标网站在短时间内发送大量请求
    - 如果有Doss攻击，则将攻击者的IP添加到防火墙的黑名单里。



#### 4、机器的磁盘满了，删日志没有反应，可能是什么问题？

在Linux或者Unix系统中，通过rm或者文件管理器删除文件将会从文件系统的目录结构上解除链接(unlink)

然而如果文件是被打开的（有一个进程正在使用），那么进程将仍然可以读取该文件，磁盘空间也一直被占用。

使用命令：**/usr/sbin/lsof|grep deleted** 

确认删除文件是否被占用

kill -9 pid值 杀掉进程



#### 5、Linux文件系统

![title](/images/2021年春招准备-Linux篇/1.png)

- `bin/sbin`：`bin` 是 `Binary` 的缩写，存放着可执行文件或可执行文件的链接（类似快捷方式），命令的存放地址

- `boot`: 这里是系统启动需要的文件，你可以看到 `grub` 文件夹，它是常见的开机引导程序。我们不应该乱动这里的文件

- `/dev`：`dev` 是 `device` 的缩写，这里存放这所有的设备文件。在 Linux 中，所有东西都是以文件的形式存在的，包括硬件设备。鼠标、键盘等等设备也都可以在这里找到。

- `/etc`: 存放很多程序的配置信息，比如包管理工具 apt

- `lib`:`lib` 是 Library 的缩写，类似于 Windows 系统中存放 `dll` 文件的库，包含 bin 和 sbin 中可执行文件的依赖。也可能出现 `lib32` 或 `lib64` 这样的目录，和 `lib` 差不多，只是操作系统位数不同而已

- `/media`: 这里会有一个以你用户名命名的文件夹，里面是自动挂载的设备，比如 U 盘，移动硬盘，网络设备等

- `/mnt`: 这也是和设备挂载相关的一个文件夹，一般是空文件夹。`media` 文件夹是系统自动挂载设备的地方，这里是你手动挂载设备的地方

- `/opt`: `opt` 是 Option 的缩写，这个文件夹的使用比较随意，一般来说我们自己在浏览器上下载的软件，安装在这里比较好

- `proc` :`proc` 是 `process` 的缩写，这里存放的是全部正在运行程序的状态信息

- `/root`: 这是 root 用户的家目录，普通用户需要授权才能访问

- `/run 和 /sys`用来存储某些程序的运行时信息和系统需要的一些信息。比如说这个文件：里面存储着一个数字，是你的显卡亮度，你修改这个数字就可以修改屏幕亮度

  - 这两个位置的数据都存储在内存中，所以一旦重启，`/run` 和 `/sys` 目录的信息就会丢失

  ```shell
  sudo vim /sys/devices/pci0000:00/0000:00:02.0/drm/card0/card0-eDP-1/intel_backlight/brightness
  ```

- `/srv`：srv 是 service 的缩写，主要用来存放服务数据

- `/tmp`: tmp 是 temporary 的缩写，存储一些程序的临时文件

- `/usr`： usr 是 Universal System Resource 的缩写，这里存放的是一些非系统必须的资源，比如用户安装的应用程序

- `/var`: var 是 variable 的缩写，这个名字是历史遗留的，现在该目录最主要的作用是存储日志（log）信息，比如说程序崩溃，防火墙检测到异常等等信息都会记录在这里

- `/home`: 这是普通用户的家目录



#### 6、CGroups

> CGroup 是 Control Groups 的缩写，将任意进程进行分组化管理的 Linux 内核功能

##### 概念解释

- 任务（task）。在 cgroups 中，任务就是系统的一个进程；

- 控制族群（control group）。控制族群就是一组按照某种标准划分的进程
  - Cgroups 中的资源控制都是以控制族群为单位实现
  - 一个进程可以加入到某个控制族群，也从一个进程组迁移到另一个控制族群
  - 一个进程组的进程可以使用 cgroups 以控制族群为单位分配的资源，同时受到 cgroups 以控制族群为单位设定的限制；

- 层级（hierarchy）。控制族群可以组织成 hierarchical 的形式，既一颗控制族群树。控制族群树上的子节点控制族群是父节点控制族群的孩子，继承父控制族群的特定的属性；

- 子系统（subsystem）。**一个子系统就是一个资源控制器**，比如 cpu 子系统就是控制 cpu 时间分配的一个控制器。子系统必须附加（attach）到一个层级上才能起作用，一个子系统附加到某个层级以后，这个层级上的所有控制族群都受到这个子系统的控制。

##### Cgroups提供了以下功能：

- 限制进程组可以使用的资源数量（Resource limiting ）。比如：memory子系统可以为进程组设定一个memory使用上限，一旦进程组使用的内存达到限额再申请内存，就会出发OOM（out of memory）
- 进程组的优先级控制（Prioritization ）。比如：可以使用cpu子系统为某个进程组分配特定cpu share。
- 记录进程组使用的资源数量（Accounting ）。比如：可以使用cpuacct子系统记录某个进程组使用的cpu时间
- 进程组隔离（Isolation）。比如：使用namespace子系统可以使不同的进程组使用不同的namespace，以达到隔离的目的，不同的进程组有各自的进程、网络、文件系统挂载空间。
- 进程组控制（Control）。比如：使用freezer子系统可以将进程组挂起和恢复。

![title](/images/2021年春招准备-Linux篇/2.png)

![title](/images/2021年春招准备-Linux篇/3.png)

CGroup 层级关系显示，CPU 和 Memory 两个子系统有自己独立的层级系统。CGroup 技术可以被用来在操作系统底层限制物理资源，起到 Container 的作用



#### 7、进程、线程、文件描述符

##### 进程

![title](/images/2021年春招准备-Linux篇/4.jpeg)

对于系统而言，进程就是一个数据结构

```cpp
struct task_struct {
    // 进程状态
    long              state;
    // 虚拟内存结构体
    struct mm_struct  *mm;
    // 进程号
    pid_t              pid;
    // 指向父进程的指针
    struct task_struct __rcu  *parent;
    // 子进程列表
    struct list_head        children;
    // 存放文件系统信息的指针
    struct fs_struct        *fs;
    // 一个数组，包含该进程打开的文件指针
    struct files_struct        *files;
};
```

##### 文件描述符

![title](/images/2021年春招准备-Linux篇/5.jpeg)

`files`，它是一个文件指针数组。一般来说，一个进程会从`files[0]`读取输入，将输出写入`files[1]`，将错误信息写入`files[2]`

**每个进程被创建时，`files`的前三位被填入默认值，分别指向标准输入流、标准输出流、标准错误流。我们常说的「文件描述符」就是指这个文件指针数组的索引**，所以程序的文件描述符默认情况下 0 是输入，1 是输出，2 是错误

Linux 中一切都被抽象成文件，设备也是文件，可以进行读和写。如果我们写的程序需要其他资源，比如打开一个文件进行读写，这也很简单，进行系统调用，让内核把文件打开，这个文件就会被放到`files`的第 4 个位置

**应用**

![title](/images/2021年春招准备-Linux篇/6.png)

##### 线程

![title](/images/2021年春招准备-Linux篇/7.png)

![title](/images/2021年春招准备-Linux篇/8.png)



#### 8、Linux增加开机启动项

- 编辑文件 `/etc/rc.local`，在文件末尾（exit 0之前）加上你开机需要启动的程序或执行的命令即可（执行的程序需要写绝对路径，添加到系统环境变量的除外）

- 自己写shell脚本，将写好的脚本（.sh文件）放到目录 `/etc/profile.d/`下，系统启动后就会自动执行该目录下的所有shell脚本

- 通过chkconfig命令设置

  - 将启动文件cp到 /etc/init.d/或者/etc/rc.d/init.d/（前者是后者的软连接）下
  - vim 启动文件，文件前面务必添加如下三行代码，否侧会提示chkconfig不支持

  ```shell
  #!/bin/sh 告诉系统使用的shell,所以的shell脚本都是这样
  #chkconfig: 35 20 80 分别代表运行级别，启动优先权，关闭优先权，此行代码必须
  #description: http server（自己随便发挥）//两行都注释掉！！！，此行代码必须
  ```

  - chkconfig --add 脚本文件名 操作后就已经添加了
  - `systemctl enable`