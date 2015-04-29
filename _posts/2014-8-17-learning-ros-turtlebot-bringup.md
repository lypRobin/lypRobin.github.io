---
layout: post
title: Turtlebot在ROS中的运行及注意事项
category: blog
tag: ros
---

ROS官网中有详细的[Turtlebot安装及Demo运行实例](http://wiki.ros.org/Robots/TurtleBot/)，不过初次上手比较混乱，各种链接跳转，这里统一一下步骤，再把遇到的一些坑填平。

### **安装Turtlebot**

这一步没什么可说的，官网分为deb安装和源码安装，这里统一使用deb安装，Ubuntu下安装下列安装包即可。这里安装指的是在Turtlebot上的计算节点，这次是用一台笔记本，后面试验ARM板来运行。为区分，在turtlebot上的pc称为turtlebot pc，在远程控制的称为workstation pc。

**turblotbot pc安装**

```
> sudo apt-get install ros-indigo-turtlebot ros-indigo-turtlebot-apps ros-indigo-turtlebot-interactions ros-indigo-turtlebot-simulator ros-indigo-kobuki-ftdi ros-indigo-rocon-remocon ros-indigo-rocon-qt-library ros-indigo-ar-track-alvar-msgs
```

安装完之后`echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc`。deb安装与源码安装更新环境变量`source /path/to/bash/setup.bash`的路径是不同的，弄乱了会导致机器人无法运行。

之后要对底盘和kinect做些设置。底盘使用的是kobuki，首先需要生成**udev rule**用来找到底盘ftdi芯片进行通信，就能在`/dev`下找到kobuki设备。

```
# 对kobuki设置
> . /opt/ros/indigo/setup.bash
> rosrun kobuki_ftdi create_udev_rules
```

由于Turtlebot默认视觉传感器是axus的xtion，要设置3D传感器为kinect

```
#对kinect设置环境变量
> export TURTLEBOT_3D_SENSOR=kinect
```

最好在`～/.bashrc`中添加以下语句，就不用总修改环境变量了：

```
> vim ~/.bashrc

export TURTLEBOT_3D_SENSOR=kinect
export TURTLEBOT_BASE=kobuki
export TURTLEBOT_STACKS=circles
```

**workstation pc安装**

同上，注意环境变量不要弄乱了。

### **网络配置**

实验是在内网环境进行的，首先保证turtlebot pc和workstation pc相互间`ping`通，安装好ssh。

**turtlebot pc**

turtlebot pc是作为`master node`的，因此turtlebot pc的`$ROS_MASTER_URI`要设置为本地，在`～/.bashrc`中添加：

```
export ROS_MASTER_URI=http://localhost:11311
export ROS_HOSTNAME=192.168.1.110   #turtlebot的ip
```


**workstation pc**

workstation pc是作为普通node的，因此它的`$ROS_MASTER_URI`要设置为turtlebot的ip，在`～/.bashrc`中添加：

```
export ROS_MASTER_URI=http://192.168.1.110:11311
export ROS_HOSTNAME=192.168.1.106   #workstation的ip
```

注意这两个环境变量在turtlebot pc和workstation pc端的正确配置，不要弄混，否则后面的测试不通。

**test**

在workstation pc端分别测试如下指令：

```
> rostopic list
和
> rostopic echo /diagnostics
```

若没有warning或error，正常输出表明网络配置正常，做`hello`测试，在workstation pc端

```
> rostopic pub -r10 /hello std_msgs/String "hello"
```

在turtlebot pc端

```
> rostopic echo /hello
```

若正常出现循环打印`hello`则网络通信正常，可进行后续的turtlebot实验。

### tuetlebot运行

在给出的例程中turtlebot是`master node`，在运行例程时要在turtlebot pc上运行起`roscore`。此外，还要运行一个`minimal.launch`来支持底盘运行。

```
> roslaunch turtlebot_bringup minimal.launch --screen
```







