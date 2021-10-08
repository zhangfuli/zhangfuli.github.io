---
title: Linux常用命令
date: 2017-09-25 10:42:30
tags: Linux
categories: Linux
---



### Everything is a file (inode 除外)
	whoami –我是谁
	pwd –我在哪
	ls –我能干什么
	ls -l 显示详细信息 -a 显示全部文件
	蓝色 –文件夹 –d
	黑色 –文件
	b c –驱动 /dev 设备文件夹
	l –软链接
	whereis +命令(ls)
	/bin /sbin–外部应用 shell自带的命令
	man +命令 –查手册
	mkdir +名字 –新建文件夹
	vi +名字 –新建vi文件
###### 命令属性
	:w –保存–:w! 强制保存
	:q –退出–:q! 强制退出
	:wq


###### 编辑属性
	gcc hello.c -o hello 执行时 ./hello若直接为hello系统视为是一个命令
	gcc -c hello.c -o hello.o
	gcc hello. -o hello
###### linux文件夹
	bin--普通命令   sbin--系统命令
	root--超级用户  home--普通用户
	proc--进程      dev--设备
	usr--内部程序   opt--外部程序
	boot--内核文件  mnt--挂载
	etc--配置文件
###### ————————————————–
	cat 显示 + 文本文件 type(dos)
###### 尽可能不要打开非文本文件
	more/less 也是显示 + 文本是否分页显示
	touch 修改创建时间
	ln
	find 查找文件
	grep 查找或不查找文本文件内容相关的信息
	sda1 –sd 串口 a 第一个硬盘的第一个分区
	fdisk -l 显示硬盘
	挂载 mount /dev/sdb1 /mnt/usb
	rm 删除文件 rm + 文件名 -f
	rm + 删除文件夹 -r(递归)
	cp/mv a—->b 文件夹 -r
	在同级文件夹下mv就是改名
	ncurse—图形界面的引擎
	gcc menu.c -o menu -lcurses //引入lib库文件
###### ————————————————
	find [path] [attr] [key]
	find /mnt -name usb /mnt/usb
	grep [key] [file] 查找文件内容
	ln (-s) source des
###### 硬链接增加节点索引号，不消耗资源
###### 修改权限
	chmod(修改权限) chown(修改属主) chgrp(修改属组)
	chmod +x ./jiaoben
	rwxrwxrwx read write execute-可执行
	u–g–o
	user group(对用户所在的组的其他用户的权限) other(组外权限)
	chomd u+x ./jiaoben
	rwxr-xr-x
	111101101 //755 特殊1、3不能出现
	chomd 755 ./jiaoben

###### 环境命令：env、echo、export
	1.env –输出环境变量
	path –命令的链接路径
	2.echo –输出环境变量
	echo $PATH —查环境变量要给钱
	3.更改PATH
	export PATH=路径:$PATH //放在前面优先级高
	归档命令：tar -xjvf -xzvf
	whereis ls
	makefile
	make clean –只保留源代码
	make –执行第一个
	make clean
	make menuconfig
	make dep –建立依赖关系
	make bzImage –编辑内核
	Makefile结构
	target:source   
	(tab)commond
	目标:源
	    命令
	标号
	make A –执行A之后B之前
	A:
	B:
	sh脚本 –chmod +x addstu
###### #!/bin/bash –用的什么shell
	< -lt
	<= -le
	> -gt
	>= -ge
	= -eq
	!= -ne
	$变量
	while [ condition ]
	do

	done

	if [ condition ]
	then

	else

	fi

	case

	esac

	i=$(($i+1)) i++
###### ——————————————————–
	/etc/shadow –密码
	/etc/passwd –用户
	/etc/group –分组
	/etc/inittab
	init 0 关机
	init 3 多用户有网络 startx
	init 6 重启
###### 进入单用户模式 改密码
	grub e
	kernal e + 1–单用户模式
	b –启动
	os：bootloader 16位
###### 作用：
###### 1.把16位的环境调成32位
###### 2.准备加载kernek内核
	kernel 32/64
	rootfs
	软盘1.44M
	service ssh start/status
	update-rc.d ssh enable

###### 进程
	ps process status
	ps -aux 当前所有的进程
	top 动态进程
	jobs 后台执行的进程
	ctrl + c 把当前进程中断
	kill -9 + pid
	ctrl + z 把当前进程搬到后台暂停
	fg 1 //1为序号
	ps process status
	ps -aux 当前所有的进程
	top 动态进程
	jobs 后台执行的进程
	ctrl + c 把当前进程中断
	kill -9 + pid
	ctrl + z 把当前进程搬到后台暂停
	fg 1 //1为序号
	pthread.h
	gcc -lpthread
###### 哲学家进餐：
	mutex lock
	wait left
	wait right
	eat
	signal left
	signal right
	unloack

