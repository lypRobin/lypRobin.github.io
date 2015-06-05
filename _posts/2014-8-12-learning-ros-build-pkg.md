---
layout: post
title: ROS笔记——建立package
category: blog
tag: ros
---

ROS是Robot Operating System，一个开放的机器人操作系统，说是OS，但依然还需要跑在Ubuntu等真正的OS上，严格说只是OS上的一个抽象层或中间件。ROS的存在省去了机器人开发过程中繁杂的驱动开发（当然还是要有驱动的，但提供了一种方便的开发形式（后面学习），或者有通用的驱动别人已经开发好了）和软件架构，只需要专注功能开发即可，ROS可以说是一个机器人的软件平台，让所有开发者都能在其中受益。

这里使用ROS indigo版本（ROS的每个版本都以不同的海龟名来命名，interesting！）

### 1. 安装

初次见面还是使用apt安装吧，后面再研究源码编译安装。按照[教程](http://wiki.ros.org/indigo/Installation/Ubuntu)一步步来就行，没遇到什么坑。

安装完之后配置环境变量:

> $ source /opt/ros/indigo/setup.bash

使用`export | grep ROS`查看环境变量：

> declare -x ROSLISP_PACKAGE_DIRECTORIES=""

> declare -x ROS_DISTRO="indigo"

> declare -x ROS_ETC_DIR="/opt/ros/indigo/etc/ros"

> declare -x ROS_MASTER_URI="http://localhost:11311"

> declare -x ROS_PACKAGE_PATH="/opt/ros/indigo/share:/opt/ros/indigo/stacks"

> declare -x ROS_ROOT="/opt/ros/indigo/share/ros"

可以看到ROS在环境变量中的一些定义，包括路径、版本、地址等等。

ROS在终端中使用一些封装后的shell命令如`rosls`、`roscd`、`roscp`等，相当于在安装路径`/opt/ros/indigo/`作为一个ROS文件系统的根目录来操作。

### 2. 建立package

ROS是以package作为程序的管理单元，每个package都是一个小的功能组件，同样功能的package集合作为一个stack。那么建立一个package就是开发ROS程序的基础了。

ROS使用`catkin`的工具来建立和编译package，`catkin`由一系列相关命令工具组成。

**2.1初始化工作空间**

一个package需要在一个工作空间中存在，首先建立并初始化workspace：

>$ mkdir -p ~/catkin_ws/src

>$ cd ~/catkin_ws/src

>$ catkin\_init\_workspace

ROS的工作空间一般分为一下几个部分：

{% highlight html %}
workspace_folder/         -- WORKSPACE
	src/                  -- 源码空间
		CMakeLists.txt        -- 顶层CMake file
		package_1/         --若干package，里面分别有自己的CMake file
	      CMakeLists.txt
	      package.xml      --package.xml用来记录包的有关信息
	      ...
	    package_n/
	      CMakeLists.txt
	      package.xml
	      ...
	build/                  -- BUILD SPACE
	    CATKIN_IGNORE         -- Keeps catkin from walking this directory
	devel/                  -- DEVELOPMENT SPACE (set by CATKIN_DEVEL_PREFIX)
	    bin/
	    etc/
	    include/
	    lib/
	    share/
	    .catkin
	    env.bash
	    setup.bash
	    setup.sh
	    ...
	install/                -- INSTALL SPACE (set by CMAKE_INSTALL_PREFIX)
	    bin/
	    etc/
	    include/
	    lib/
	    share/
	    .catkin             
	    env.bash
	    setup.bash
	    setup.sh
	    ...
{% endhighlight %}

初始化后src目录下只有一个CMakeList.txt文件用于cmake编译。继续，建立一个包：

> $ catkin\_create\_pkg test\_pkg std_msgs 

后面的std_msgs是新建包的一个依赖包。

进入到`src`目录下开始编译

>$ cd ~/catkin_ws/

>$ catkin_make

编译完之后，新建包下面就有CMakeList.txt和package.xml文件了，可以修改package.xml文件中的包信息。

最后`$ source devel/setup.bash`加入环境变量，这里注意教程中使用`$ . ~/catkin_ws/devel/setup.bash`,不知为何使用了"."，是不成功的。这样一个空包就编译和建立好了。

可以分别使用

> $ rospack depends1 test_pkg

和

> $ rospack depends test_pkg

来查看新建包的直接依赖包和间接依赖包。























