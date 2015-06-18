---
title: ROS从Kinect中读取点云数据
layout: post
category: blog
tags: [ros, pcl, kinect]
---

在ROS中点云数据是利用`sensor_msgs::PointCloud2`消息进行数据传递，而在PCL中点云数据是以pcl::PointCloud\<T\>数据类型存在，在[这篇文档](http://wiki.ros.org/pcl/Overview)中有说明，并且两者间可以进行转换。在kinect开发过程中利用点云数据多些，因此使用pcl::PointCloud\<T\>数据类型为主。

### *1. 利用openni.launch直接读取*

以一个小例子对ROS读取kinect点云数据进行说明。

```
#建立工作空间和package
$ catkin_create_pkg my_pcl_capture pcl_conversions pcl_ros roscpp sensor_msgs 
```

在`package.xml`文件中加入依赖

```
  <build_depend>libpcl-all-dev</build_depend>
  <run_depend>libpcl-all</run_depend>
```

建立`src/capture_pcl.cpp`文件

{% highlight cpp %}
#include <ros/ros.h>
#include <sensor_msgs/PointCloud2.h>
#include <pcl_conversions/pcl_conversions.h>
#include <pcl/point_cloud.h>
#include <pcl/point_types.h>

ros::Publisher pub;

void cloud_cb (const sensor_msgs::PointCloud2ConstPtr& input)
{
  // Create a msg for the point cloud data.
  sensor_msgs::PointCloud2 output;

  // just transfer data
  output = *input;

  // Publish the data.
  pub.publish (output);
}

int main (int argc, char** argv)
{
  // Initialize ROS
  ros::init (argc, argv, "my_pcl_tutorial");
  ros::NodeHandle nh;

  // Create a ROS subscriber for the input point cloud
  ros::Subscriber sub = nh.subscribe ("input", 1, cloud_cb);

  // Create a ROS publisher for the output point cloud
  pub = nh.advertise<sensor_msgs::PointCloud2> ("output", 1);

  // Spin
  ros::spin ();
}

{% endhighlight %}

在`CMakeList.txt`文件中加入

```
add_executable(capture_pcl src/capture_pcl.cpp)
target_link_libraries(capture_pcl ${catkin_LIBRARIES})
```

编译后将`setup.bash`文件生效

```
$ source devel/setup.bash
```

连接好kinect，另开终端运行`roscore`Master节点，在当前终端运行

```
$ roslaunch openni_launch openni.launch
$ rosrun my_pcl_capture capture_pcl input:=/camera/depth/points
```

另开终端，运行

```
$ rosrun rviz rviz
```

在`rviz`中添加PointCloud2形式的消息，消息来源选择`/output`，这时就能看到kinect获取的点云数据。

### *2. PCL获取点云数据*

*Point Cloud Talker点云获取*

点云数据获取使用`pcl`中的OpenNIGrabber来完成。建立`pc_talker.cpp`

{% highlight cpp %}
#include <ros/ros.h>
#include <pcl/point_types.h>
#include <pcl/visualization/pcl_visualizer.h>    
#include <pcl/io/openni_grabber.h>
#include <pcl/common/time.h>


typedef pcl::PointXYZRGBA PointT;
typedef pcl::PointCloud<PointT> PointCloudT;

// PCL viewer //
// pcl::visualization::PCLVisualizer viewer("PCL Viewer");

// Mutex: //
boost::mutex cloud_mutex;

enum { COLS = 640, ROWS = 480 };

void cloud_cb_ (const PointCloudT::ConstPtr &callback_cloud, PointCloudT::Ptr& cloud,
    bool* new_cloud_available_flag)
{
  cloud_mutex.lock ();    // for not overwriting the point cloud from another thread
  *cloud = *callback_cloud;
  *new_cloud_available_flag = true;
  cloud_mutex.unlock ();
}


int main (int argc, char** argv)
{
  ros::init(argc,argv, "pc_talker");
  ros::Publisher pub;
  ros::NodeHandler nh;
  pub = nh.advertise<sensor_msgs::PointCloud2>("point_cloud", 1000);
 
  // Read Kinect live stream:
  PointCloudT::Ptr cloud (new PointCloudT);
  bool new_cloud_available_flag = false;
  pcl::Grabber* interface = new pcl::OpenNIGrabber();
  boost::function<void (const pcl::PointCloud<pcl::PointXYZRGBA>::ConstPtr&)> f =
      boost::bind (&cloud_cb_, _1, cloud, &new_cloud_available_flag);
  interface->registerCallback (f);
  interface->start ();

  // Wait for the first frame:
  while(!new_cloud_available_flag) 
    boost::this_thread::sleep(boost::posix_time::milliseconds(1));
  new_cloud_available_flag = false;

  cloud_mutex.lock ();    // for not overwriting the point cloud


  cloud_mutex.unlock ();    

   // Initialize new viewer:
  pcl::visualization::PCLVisualizer viewer("PCL Viewer");          // viewer initialization

    // For timing:
  static unsigned count = 0;
  static double last = pcl::getTime ();

  // Main loop:
  while (!viewer.wasStopped())
  {
    if (new_cloud_available_flag && cloud_mutex.try_lock ())    // if a new cloud is available
    {
      new_cloud_available_flag = false;

	  // Publish one frame point cloud data
	  pub.Publish(cloud);

      // Display average framerate:
      if (++count == 30)
      {
        double now = pcl::getTime ();
        std::cout << "Average framerate: " << double(count)/double(now - last) << " Hz" <<  std::endl;
        count = 0;
        last = now;
      }
      cloud_mutex.unlock ();
    }
  }
  ros::spin();
  return 0;
}


{% endhighlight %}


*point cloud listener*

建立`pc_listener.cpp`文件

{% highlight cpp %}
#include <ros/ros.h>
#include <pcl/point_types.h>
#include <pcl/visualization/pcl_visualizer.h>    
#include <pcl/io/openni_grabber.h>
#include <pcl/common/time.h>
#include <sensor_msgs/PointCloud2.h>
#include <pcl_conversions/pcl_conversions.h>
#include <pcl/point_cloud.h>

static unsigned cnt = 0;

// callback function parameters
struct callback_arg{
	double last;
	pcl::visualization::PCLVisualizer viewer;
};

void cloud_callback_(const pcl::PointCloud<pcl::PointXYZRGBA>::ConstPtr &callback_cloud,
					void* arg)
{
	// show point cloud data in visualization
	struct callback_arg *data = (callback_arg*)arg;
	data->viewer.removeAllPointClouds(); 
	data->viewer.removeAllShapes();
	pcl::visualization::PointCloudColorHandlerRGBField<pcl::PointXYZRGBA> rgb(callback_cloud);
	data->viewer.addPointCloud<pcl::PointXYZRGBA> (callback_cloud, rgb, "turtlebot_cloud");
	data->viewer.spinOnce();

	// calculate frame rate
	if(30 == ++cnt)
	{
		double now = pcl::getTime();
		double rate = (double)cnt / (double)(now - data->last);
		ROS_INFO("Average Framerate: %f Hz", rate);
		data->last = now;
	}
}



int main(int argc, char** argv)
{
	ros::init(argc, argv, "pcl_listener");
	uint32_t queue_size = 1000;
	ros::NodeHandle nh;
	std::string topic = nh.resolveName("pcl_topic");

	// setup a point cloud visualization viewer
	pcl::visualization::PCLVisualizer viewer("PCL Viewer");          // viewer initialization
	viewer.setCameraPosition(0,0,-2,0,-1,0,0);
	struct callback_arg arg;
	arg.last = pcl::getTime();
	arg.viewer = viewer;

	// Subscribe the point cloud topic
	ros::Subscriber sub = nh.subscribe<pcl::PointCloud<pcl::PointXYZRGBA> > (topic, queue_size,	
		boost::bind(&cloud_callback_, _1, boost::cref((void*)&arg)));

	ros::spin();
	return 0;
}

{% endhighlight %}

编译完成后，分别运行发送与监听，在监听端即可在窗口中看到kinect发送的点云数据，在终端中打印帧率。同样在`rviz`中将topic源设为`point_cloud`即可在`rviz`中显示点云数据。

以上显示的点云数据均为RGB彩色图像，也可在显示点云时采用`pcl::visualization::PointCloudGeometryHandlerXYZ<pcl::PointXYZRGBA> xyz(cloud);`即可获取深度数据。








