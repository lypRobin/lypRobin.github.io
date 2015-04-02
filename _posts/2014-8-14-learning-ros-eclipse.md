---
title: 在eclipse中开发ROS
layout: post
---

根据教程学习了ROS开发例子，但开发比较复杂的工程或有序组织文件还是要有个IDE比较好，那神器eclipse就当人不让了，在eclipse中配置ROS开发环境也很简单。

首先安装Eclipse，然后安装CDT和/或PyDev插件，完事后就开始配置ROS在Eclipse下的开发环境了。

#### 使用**roscreate-pkg**命令创建包

首先新建一个package，`roscd`进入到这个包（注意已经先将workspace `source devel/setup.bash`），注意，如果使用`roscreate-pkg`命令创建包，则包路径下会有CMakeList.txt、mainpage.dox、manifest.xml、Makefile文件，这时直接运行命令：

```
 make eclipse-project
```
package路径下多了一个lib文件夹。

#### 使用**catkin_create_pkg**命令创建包

若使用该命令，则包路径下只有CMakeList.txt和package.xml文件(如果有依赖包，则有`include`和`src`文件夹)，使用如下命令生成Eclipse所需要的文件：

```
cmake -G"Eclipse CDT4 - Unix Makefiles"
```

完毕后包目录下会生成很多相关目录与文件。

打开Eclipse，在File -> import中导入文件，定位到包目录下，将所有文件导入。之后在project->properties中，C/C++ Make Project--> Environment添加如下环境变量。

```
ROS_ROOT
ROS\_PACKAGE\_ROOT
PYTHONPATH
PATH 
```

在工程下新建一个src文件夹，源码文件都包含在这个文件夹中，编好代码后，还要修改CMakeList.txt和package.xml文件，注意CMakeList.txt文件中需要修改的内容。

```
include_directories(
  include  
  ${catkin_INCLUDE_DIRS}
)

add_executable(name_of_node src/name_of_node.cpp)

add_dependencies(name_of_node name_of_node_generate_messages_cpp)

target_link_libraries(name_of_node  ${catkin_LIBRARIES})

```

点击build按钮或`Ctrl+B`进行编译。编译没问题就可以运行了，运行前要注意，在Run-->Run Configuration-->C/C++ Application中，右键单击**New**或**双击**建立新的application，在**Main**选项卡中**Search project**选择编译好的可执行文件，之后在**Environment**选项卡还要加入环境变量

```
ROS_ROOT
ROS_MASTER_URI
```

就可以运行了！


























