---
title: Grasscutter3.7
date: 2023-06-29 13:25:02
tags: java
---

[Grasscutter](https://github.com/Grasscutters/Grasscutter)是一个开源的专为某个二次元游戏运行的Java后端

## Grasscutter 3.7 那个游戏私服快速搭建指南

#### 环境配置

​	Grasscutter用Java搭建服务器，再用MongoDB存储用户数据

- 获取Java17
- 获取MongoDB社区版

##### 	获取 Java17

​		如果已经安装了Java17或者更高版本可以直接使用安装好的Java环境

​		下载地址：https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html

​		选择Windows x64 MSI Installer 下载，如果不想安装在C盘，这里需要记住Java安装的路径,默认的路径为

​		`C:\Program Files\Java\jdk-17\bin\java.exe`

##### 	获取[MongoDB社区版](https://www.mongodb.com/try/download/community)

​		4.0以上即可，选择win64.msi进行安装，如果安装过程中缓慢可以取消Install MongoDB compass工具安装，需要时再自己安装

​		检测MongoDB服务是否开启：

​		在CMD中输入services.msc找到MongoDB服务是否开启，若未开启，则手动启动服务

### 使用Cultvation进行本地快速部署

#### 获取Cultvation(不是照抄，只是还没写完)

​		下载[最新的Cultivation版本](https://github.com/Grasscutters/Cultivation/releases/latest)（使用以“.msi”为后缀的安装包）

- 以管理员身份打开Culivation，按右上角的下载按钮。
- 点击“下载 Grasscutter 一体化”
- 点击右上角的齿轮
- 将游戏安装路径设置为你游戏所在的位置。
- 将自定义Java路径设置为`C:\Program Files\Java\jdk-17\bin\java.exe`
- 保持所有其它设置为默认值
- 点击“启动”按钮旁边的小按钮。
- 点击“启动”按钮。
- 随便想一个用户名登录，不需要密码。

### 服务端部署

#### 获取Grasscutter

Grasscutter有两种获取方式：

- 使用Git获取

- 在GitHub获取

##### 使用Git获取

打开CMD，输入

`git clone --recurse-submodules https://github.com/Grasscutters/Grasscutter.git`

便可在C盘用户文件夹内创建一个Grasscutter的文件夹，如果不想在用户文件夹内生成则可以cd跳转到自定义目录

##### 使用Github获取

进入[Grasscutter](https://github.com/Grasscutters/Grasscutter)官方站点https://github.com/Grasscutters/Grasscutter

点击Code里的Download ZIP进行下载，之后解压到自己想要的文件夹内即可

#### 编译服务端

在你的Grasscutter根目录内点击地址栏，输入CMD打开命令提示符输入以下指令：

```
.\gradlew.bat 
.\gradlew jar 
```

其中`.\gradlew.bat `设置开发环境`.\gradlew jar `开始编译
