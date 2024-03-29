---
title: Ceph 分布式文件系统简介
date: 2020-12-02T02:47:34+08:00
authors: ["Neal"]
categories: [Linux]
tags: [Linux]
---

![blog-ceph-intr](https://pengshp.coding.net/p/images/d/images/git/raw/master/blog-ceph-intr.jpg "Ceph")

Ceph 是一种使用广泛的分布式文件系统



<!--more-->

# 存储分类

## 本地文件系统

ntfs(wind)、ext2、ext3、ext4、xfs          

ext2不带日志，3和4带有日志：文件系统的日志作用（防止机器突然断电）：所有的数据在给磁盘存数据之前会先给文件系统的日志里面存一份，防止机器突然断电之后数据没有存完，这样它还可以从日志里面重新将数据拷贝到磁盘。

## 网络文件系统

nfs           网络文件系统--称之为nas存储（网络附加存储）

## 分布式文件系统

- hdfs          分布式网络文件系统         

- glusterfs     分布式网络文件系统，不需要管理服务器         

- ceph          分布式网络文件系统，块存储，对象存储

# 分布式文件系统架构

```shell
							client
                                 |
                             namenode   
                                 |
                 ------------------------------------
                 |               |                  |
               datanode        datanode            datanode
```

namenode 元数据服务器-管理服务器，存储这个文件的数据存放的位置信息

datanode 存储数据，数据节点

# 分布式文件系统的特性

- 可扩展         

  分布式存储系统可以扩展到几百台甚至几千台的集群规模，而且随着集群规模的增长，系统整体性能表现为线性增长。分布式存储的水平扩展有以下几个特性：           

  1) 节点扩展后，旧数据会自动迁移到新节点，实现负载均衡，避免单点故障的情况出现;           

  2) 水平扩展只需要将新节点和原有集群连接到同一网络，整个过程不会对业务造成影响;          

- 低成本         

  分布式存储系统的自动容错、自动负载均衡机制使其可以构建在普通的PC机之上。          

- 易管理         

  可通过一个简单的WEB界面就可以对整个系统进行配置管理，运维简便，极低的管理成本。

## 块存储的特点：     

1.主要是将裸磁盘空间映射给主机使用的，共享的最小单位是块     

2.使用的交换机是光纤交换机价格贵成本高     

3.性能最好，扩展性好     

4.不能做文件系统的共享

最典型的就是SAN（storage area network）(存储区域网)----有一个局域网里面有一个交换机， 交换机上面连着服务器，所有服务器都是专业存储的设备，他们组成一个存储区域网，当我们用的时候只需要在 这个区域网里面拿空间使用

## 对象存储                        

### 为什么需要对象存储？     

首先，一个文件包含了属性（术语叫metadata，元数据，例如该文件的大小、修改时间、存储路径等）以及内容（以下简称数据）。      

而对象存储则将元数据独立了出来，控制节点叫元数据服务器（服务器+对象存储管理软件），里面主要负责存储对象的属性（主要是对象的数据被打散存放到了那几台分布式服务器中的信息），而其他负责存储数据的分布式服务器叫做OSD，主要负责存储文件的数据部分。当用户访问对象，会先访问元数据服务器，元数据服务器只负责反馈对象存储在哪些OSD，假设反馈文件A存储在B、C、D三台OSD， 那么用户就会再次直接访问3台OSD服务器去读取数据。      

由于是3台OSD同时对外传输数据，所以传输的速度就加快了。当OSD服务器数量越多，这种读写速度的提升就越大，通过此种方式，实现了读写快的目的。      

另一方面，对象存储软件是有专门的文件系统的，所以OSD对外又相当于文件服务器，那么就不存在文件共享方面的困难了，也解决了文件共享方面的问题。      

#所以对象存储的出现，很好地结合了块存储与文件存储的优点。  

### 优点：         

1. 具备块存储的读写高速。         

2. 具备文件存储的共享等特性。  

### 使用场景： (适合更新变动较少的数据)         

1. 图片存储。         

2. 视频存储。
