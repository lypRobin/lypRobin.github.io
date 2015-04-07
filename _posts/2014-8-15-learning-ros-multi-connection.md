---
layout: post
title: ROS-多机通信
category: blog
tag: ros
---

ROS适用于多机通信，在同一局域网中需要注意配置一下几点。

**1、机器间相互通信**

设有两台机器C1、C2分别安装了ROS，C1、C2间能够相互`ping`通。

**2、设置ROS参数**

假设C1的ip为`192.168.1.101`，C2的ip为`192.168.1.102`，在C1中设置：

```
$ export ROS_IP=192.168.1.101
```

在C2中设置：

```
$ export ROS_IP=192.168.1.102
```

假设C1当做`master`节点，在C2中设置：

```
$ export ROS_MASTER_URI=http://192.168.1.101:11311
```

若要使用`hostname`访问，则在C1和C2中分别设置：

```
$ export ROS_HOSTNAME=C1_hostname
```

```
$ export ROS_HOSTNAME=C2_hostname
```

在C2中可设置：

```
$ export ROS_MASTER_URI=http://C1_hostname:11311
```

**测试**

在C1中运行

```
$ roscore
```

另开一个终端，运行：

```
$ rosrun rospy_tutorials listener.py
```

在C2中运行

```
$ rosrun rospy_tutorials talker.py
```

则可在两台机器看到talker与listener间相互通信。


















