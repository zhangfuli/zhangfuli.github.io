----
title: windows下的包管理器chocolatey
date: 2015-06-02
tags: 环境
categories: 环境
----

### 前言
###### Linux安装软件都有软件包管理器，安装卸载软件很方便，比如ubuntu下只需要apt-get install 		php就可以等待php的安装完成与环境变量的注册。而win很多东西就很麻烦，尤其是和开发相关的，比如git，比如php，比如nginx的安装。 
然后就有学长给我安装了chocolatey。win下的软件包管理器。
官网是：<a href="https://chocolatey.org/">https://chocolatey.org/</a>(不翻墙打开有点困难，好像是因为用了Google字体")

### 安装
###### 在管理员模式cmd下复制这段代码就可以自动运行
	@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"

###### 安装完成后，命令行安装的软件就可以了
###### 以googlechrome为例
	choco install googlechrome