---
title: TarsGo windows系统下的踩坑
date: 2019-07-12 10:42:30
tags: Go
categories: Go
---

# TarsGo windows系统下的踩坑
### 安装Go语言
下载安装Go([go安装包位置]([https://golang.org/dl/](https://golang.org/dl/)))，并在环境变量中设置两个关键路径如下：
```
GOROOT = Go的系统安装目录
GOPATH = Go的工作路径
```
Go语言推荐目录结构:  [project-layout](https://github.com/golang-standards/project-layout)
TarsGo会生成一个默认的目录结构

### Go的编译环境JetBrains GoLand

下载安装[JetBrains GoLand](https://www.jetbrains.com/go/)
安装完成后需要在Settings内设置GOROOT、Global GOPATH、Project GOPATH目录

### 使windows支持linux命令

- git bash，使用[chocolaey]([https://chocolatey.org](https://chocolatey.org/))安装git
- mingw64， [https://stackoverflow.com/questions/32127524/how-to-install-and-use-make-in-windows](https://stackoverflow.com/questions/32127524/how-to-install-and-use-make-in-windows)
- make，使用[chocolaey]([https://chocolatey.org](https://chocolatey.org/))安装make

### TarsGo

- 通过[TarsGo快速指南](https://github.com/TarsCloud/TarsGo/blob/master/docs/tars_go_quickstart.md)拉取TarsGo项目并编译Tars协议转Golang工具
 - **如下所示修改create_tars_server.sh**
 ```
export GOPATH=$(echo $GOPATH | cut -f1 -d ':')
if [ "$GOPATH" == "" ]; then
echo "GOPATH must be set"
exit 1
fi

......

for RENAMEFILE in `ls `     //该句会报没有rename命令的错误无关紧要, 但手动修改文件名
do
rename "Server" "$SERVER" $RENAMEFILE
rename "Servant" "$SERVANT" $RENAMEFILE
done
 ```
```
export GOPATH=$(echo $GOPATH)
if [ "$GOPATH" == "" ]; then
    echo "GOPATH must be set"
    exit 1
fi
 ```
- 运行create_tars_server.sh创建服务
```
sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh [App] [Server] [Servant]
例如： 
sh $GOPATH/src/github.com/TarsCloud/TarsGo/tars/tools/create_tars_server.sh TestApp HelloGo SayHello
```
- 该脚本会在$(GOPATH)/src/生成项目[App]，暂时未到git bash 支持**rename**的方法，暂且手动修改\$(GOPATH)/src/[App]/[Server]下生成的文件名
```
Servant.tars---->[Servant].tars  
ServantImp.go---->[Servant]Imp.go
Server.conf---->[Server].conf
Server.go---->[Server].go
```
- 若根据[TarsGo快速指南](https://github.com/TarsCloud/TarsGo/blob/master/docs/tars_go_quickstart.md)的提示编写代码，**main包下的func main()调用同一包但是不同文件下的SayHelloImp会出现引用不到的问题,故将SayHelloImp放入package imp包下**
- 编写完成服务端代码[TarsGo快速指南](https://github.com/TarsCloud/TarsGo/blob/master/docs/tars_go_quickstart.md)会提示运行make, 在此之前对**$(GOPATH)/src/github.com/TarsCloud/TarsGo/tars/makefile.tars**进行修改
```
GO = ${GOROOT}/bin/go
libpath=${subst :, ,$(GOPATH)}
TARS2GO := $(firstword $(subst :, , $(shell go env GOPATH)))/bin/tars2go
J2GO_FLAG := -outdir=vendor ${J2GO_FLAG}
```
```
GO = $(shell go env GOROOT)/bin/go
libpath=${subst :, ,$(GOPATH)}
TARS2GO     := $(firstword $(shell go env GOPATH))/bin/tars2go
J2GO_FLAG   := -outdir=vendor ${J2GO_FLAG} 
```
- 运行make && make tar会生成[Server]的文件
- **启动运行./[Server] --config = [Server].config**
- 在$(GOPATH)/src/[App]/[Server]/client下编写客户端代码测试, 注意要与[Server].config的端口一致
