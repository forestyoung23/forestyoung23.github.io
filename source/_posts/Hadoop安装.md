---
title: Hadoop
date: 2020-05-09 00:45:41
categories: 大数据
tags: Hadoop
---

![](https://oss.forestyoung.top/river-5134258_1920.jpg)

<!--more-->

### jdk安装

系统：CentOS

```shell
# 查看yum包含的jdk版本
[root@ ~]# yum search java
# 安装JDK
[root@ ~]# yum install java-1.8.0-openjdk-devel.x86_64
# 配置环境变量前，需要定位到jdk安装目录
[root@ ~]# which java
/usr/bin/java
# 再找到/usr/bin/java的超链接位置发现还是超链接
[root@ ~]# ls -lrt /usr/bin/java
/usr/bin/java -> /etc/alternatives/java
# 对超链接再次查询，发现最终位置
[root@ ~]# ls -lrt /etc/alternatives/java
/etc/alternatives/java -> /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el8_1.x86_64/jre/bin/java
# 配置环境变量
[root@ ~]# vim /etc/profile
# 配置下列信息，JAVA_HOME需要配置为刚查询出来的jdk目录
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.el8_1.x86_64/
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
# 使配置生效
[root@ ~]# source /etc/profile
```

### Hadoop安装

```shell
# 下载包
[root@ ~]# wget http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-3.1.3/hadoop-3.1.3.tar.gz
# 解压
[root@ ~]# tar -zxvf hadoop-3.1.3.tar.gz
# 配置
[root@ ~]# cd /hadoop-3.1.3/etc/hadoop
# 三个配置文件配置core-site.xml，hdfs-site.xml，mapred-site.xml
```



## Hadoop运行模式

- 本地模式
- 伪分布式
- 完全分布式
- 

