---
title: ROS及PCL中的callback函数添加参数
layout: post
category: blog
tags: [ros, pcl]
---

在ROS中节点订阅一个消息一般使用

```
void callback(const sensor_msgs::MessageType &msg)

sub = nh.subscribe("topic", callback, size);
```

当回调函数中需要有额外参数时这样直接调用方式就出问题了。一般会将callback函数作为一个类的成员函数使用,参数也作为类中变量从而可以在回调函数内部获取参数
### *1. subscribe的callback函数参数获取*

将Subscriber作为一个类，并将callback函数作为其中的成员函数，例如：

```
class Subscriber
{
private:
	char* str;
	ros::NodeHandle nh;
	ros::Subscriber sub;
public:
	void callback(const sensor_msgs::MessageType::ConstPtr &msg)
	{
		str = "HelloWorld";
	}

	void run()
	{
		uint32_t queue_size = 1000;
		sub = nh.subscribe<sensor_msg::MessageType>("topic", queue_size, &Subscriber::callback, this);
		ros::spin();
	}
}

int main(int argc, char** argv)
{
	ros::init(argc, argv, "node");
	Subcriber s;
	s.run();
	
	return 0;
}
```

### *2. 主程序循环，按`Ctrl-C`无法退出程序问题*

在ROS中主程序循环应以`ros::ok()`为判读条件，可使得程序在运行是使用`Ctrl-C`退出，如：

```
class Publisher
{
private:
	char* str;
	ros::NodeHandle nh;
	ros::Publisher pub;
public:
	void run()
	{
		uint32_t queue_size = 1000;
		sensor_msgs::MessageType msg;

		pub = nh.advertise<sensor_msg::MessageType>("topic", queue_size);
		
		while(ros::ok())
		{
			pub.Publish(msg);
		}
		
		ros::spin();
	}
}

int main(int argc, char** argv)
{
	ros::init(argc, argv, "node");
	Publisher p;
	p.run();
	
	return 0;
}

```














