---
title: docker运行mysql镜像并用navicat链接
date: 2019-10-10 15:34:46
tags:
---





1、运行下载mysql镜像，同时挂载数据

```shell
docker run -it --rm --name mysql -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -v ~/docker/mysql/data:/var/lib/mysql -d mysql
```

2、查看已运行的容器，确保mysql镜像运行成功

```shell
docker ps -a |grep mysql
```

3、进入mysql镜像修改远程链接权限

```shell
docker exec -it mysql bash
root@a42f31094df5:/# 
root@a42f31094df5:/# mysql -uroot -p
mysql> ALTER user 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';  
mysql> FLUSH PRIVILEGES;
```

4、可以测试远程访问啦