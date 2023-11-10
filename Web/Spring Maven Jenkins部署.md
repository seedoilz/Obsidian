---
aliases: []
title: Spring Maven Jenkins部署
date created: 十一月 8日 2023, 1:44:55 下午
date modified: 十一月 8日 2023, 1:47:50 下午
---
#bug

![image.png](https://typora-tes.oss-cn-shanghai.aliyuncs.com/picgo/20231108134535.png)

报错不支持，java17 本质原因是 yum install 只安装了JRE，而没有安装JDK，所以解决办法就是如下：
```shell
sudo yum install java-17-openjdk-devel
```