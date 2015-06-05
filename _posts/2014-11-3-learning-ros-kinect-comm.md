---
title: ARM平台kinect与PC端ROS通信
layout: post
catalog: blog
tags: [ros, 3dsensor]
---

在A80平台下使用`Sample-NiSimpleCreate`命令能够读取kinect数据，则利用该例程源码与ROS做`publisher`与`subscriber`通信，在`OpenNI/Samples/NiSimpleCreate`目录中复制`NiSimpleCreate.cpp`中的内容，参照`ROS/tutorials`中[Writing a Simple Publisher and Subscriber (C++)](http://wiki.ros.org/ROS/Tutorials/WritingPublisherSubscriber%28c%2B%2B%29)的例程做如下修改。

### *kinect_talker*

在A80上新建工作空间文件夹并初始化

```
> mkdir -p kinect_ws/src
> cd kinect_ws/src
> catkin_init_workspace
> cd kinect/src
> catkin_create_pkg kinect_talker std_msgs roscpp
> cd kinect_talker/src
```

在`src`路径下建立`kinect_talker.cpp`文件：

{% highlight cpp %}

/*****************************************************************************
*                                                                            *
*  OpenNI Kinect communicate with ROS                                        *
*                                                                            *
*  This file is the Kinect talker with ROS on ARM A80 board.                 *
*                                                                            *
*  Licensed under the Apache License, Version 2.0 (the "License");           *
*  you may not use this file except in compliance with the License.          *
*  You may obtain a copy of the License at                                   *
*                                                                            *
*      http://www.apache.org/licenses/LICENSE-2.0                            *
*                                                                            *
*  Unless required by applicable law or agreed to in writing, software       *
*  distributed under the License is distributed on an "AS IS" BASIS,         *
*  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.  *
*  See the License for the specific language governing permissions and       *
*  limitations under the License.                                            *
*                                                                            *
*****************************************************************************/
//---------------------------------------------------------------------------
// Includes
//---------------------------------------------------------------------------
#include <XnOpenNI.h>
#include <XnLog.h>
#include <XnCppWrapper.h>

#include "ros/ros.h"  
#include "std_msgs/String.h"    
#include <sstream>

//---------------------------------------------------------------------------
// Macros
//---------------------------------------------------------------------------
#define CHECK_RC(rc, what)											\
	if (rc != XN_STATUS_OK)											\
	{																\
		printf("%s failed: %s\n", what, xnGetStatusString(rc));		\
		return rc;													\
	}

//---------------------------------------------------------------------------
// Code
//---------------------------------------------------------------------------

using namespace xn;

int main(int argc, char **argv)
{
	XnStatus nRetVal = XN_STATUS_OK;
	Context context;

    ros::init(argc, argv, "talker");  
    ros::NodeHandle n;  
    ros::Publisher chatter_pub = n.advertise<std_msgs::String>("kinect_comm", 1000);  
    ros::Rate loop_rate(10);  

	nRetVal = context.Init();
	CHECK_RC(nRetVal, "Initialize context");

	DepthGenerator depth;
	nRetVal = depth.Create(context);
	CHECK_RC(nRetVal, "Create depth generator");

	nRetVal = context.StartGeneratingAll();
	CHECK_RC(nRetVal, "StartGeneratingAll");

	DepthMetaData depthMD;
	while (!xnOSWasKeyboardHit())
	{
        std_msgs::String msg;  
        std::stringstream ss;

		nRetVal = context.WaitOneUpdateAll(depth);
		if (nRetVal != XN_STATUS_OK)
		{
			printf("UpdateData failed: %s\n", xnGetStatusString(nRetVal));
			continue;
		}

		depth.GetMetaData(depthMD);
		
        ss << "Frame " << depthMD.FrameID();  
        ss << "  Middle point is: " <<  depthMD(depthMD.XRes() / 2, depthMD.YRes() / 2);  
        msg.data = ss.str();  
        ROS_INFO("%s", msg.data.c_str());  
  
        chatter_pub.publish(msg);  
  
        ros::spinOnce();  
        loop_rate.sleep(); 
	}

	depth.Release();
	context.Release();

	return 0;
}

{% endhighlight %}

### *kinect_listener*


在PC端新建工作空间并初始化

```
> mkdir -p kinect_ws/src
> cd kinect_ws/src
> catkin_init_workspace
> cd kinect/src
> catkin_create_pkg kinect_listener std_msgs roscpp
> cd kinect_listener/src
```

在`src`目录下新建`kinect_listener.cpp`：

{% highlight C++ %}

#include "ros/ros.h"  
#include "std_msgs/String.h"  
  

void chatterCallback(const std_msgs::String::ConstPtr& msg)  
{  
  ROS_INFO("I heard: [%s]", msg->data.c_str());  
}  
  
int main(int argc, char **argv)  
{  
  ros::init(argc, argv, "listener");  
  ros::NodeHandle n;  
  
  ros::Subscriber sub = n.subscribe("kinect_comm", 1000, chatterCallback);  
  
  ros::spin();  
  
  return 0;  
}

{% endhighlight %}

### *修改`CMakeList.txt`文件*

在A80端，打开`CMakeList.txt`并做如下修改：

```
include_directories ("/usr/include/ni/")
add_executable(kinect_talker src/kinect_talker.cpp)  
add_dependencies(kinect_talker kinect_talker_generate_messages_cpp)
target_link_libraries(kinect_talker OpenNI)
```

在PC端的`CMakeList.txt`中

```
add_executable(kinect_listener src/kinect_listener.cpp)   
add_dependencies(kinect_listener kinect_listener_generate_messages_cpp)
target_link_libraries(kinect_talker ${catkin_LIBRARIES})
```

最后在A80端和PC端分别`catkin_make`编译，分别运行`rosrun kinect_talker kinect_talker`和`rosrun kinect_listener kinect_listener`，可在PC端收到kinect发送的Frame和MidPoint数据。








