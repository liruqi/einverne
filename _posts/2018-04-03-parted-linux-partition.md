---
layout: post
title: "每天学习一个命令：parted 在 Linux 下给硬盘分区"
tagline: ""
description: ""
category: 学习笔记
tags: [linux, parted, gparted, partition, clonezilla,]
last_updated:
---

`parted` 是 GNU 组织开发的一款功能强大的磁盘分区和分区大小调整工具，命令可以对磁盘进行分区和管理，和 fdisk 相比，能够支持 2T 以上磁盘。它可以处理最常见的分区格式，包括：ext2、ext3、fat16、fat32、NTFS、ReiserFS、JFS、XFS、UFS、HFS 以及 Linux 交换分区。

功能特点：

- 能够创建、清除、调整、移动和复制 ext2, ext3, ext4, linux swap, fat32, ntfs 等分区
- 能够重新分配磁盘使用情况

`parted` 有两种使用方式

## 命令行方式
定义分区表格式

    parted -s /dev/sda mklabel gpt

划分主分区

    parted -s /dev/sda mkpart primary ext4 1 10G

划分逻辑分区

    parted -s /dev/sda mkpart logic 10G 20G

查看分区情况

    parted -s /dev/sda p

直接使用一行命令来完成分区操作

    parted /dev/sdb mklabel gpt mkpart 1 ext4 1 5T


## 交互命令方式
使用 `parted /dev/sdb` 来进入对 `/dev/sdb` 硬盘的管理。

在交互命令下可以使用如下命令

交互命令               |  功能
-----------------------|------------------------
mklabel gpt            | 定义分区表格式，常用的有 msdos 和 gpt 分区格式，2T 以上硬盘选用 gpt
mkpart p1             | 创建第一个分区，名称为 p1，在使用该命令后会选择分区的格式，分区起始位置，分区的结束位置
print                  | 查看当前分区情况
rm                     | 删除分区，之后会选择想要删除的分区‘
help                 | 帮助

在划分分区之后，可以使用

    mkfs.ext4 /dev/sdb1

来针对 sdb 磁盘上第一块分区进行格式化。然后挂载分区 `mount /dev/sdb1 /mnt/sdb1`。

对于一块新的硬盘，如果没有 GUI 的界面来进行格式化，就需要用到这个命令了。


