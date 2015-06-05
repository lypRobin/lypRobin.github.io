---
title: kinect在ARM平台安装移植
layout: post
catalog: blog
tags: [ros, 3dsensor]
---

前文说了ROS向ARM移植的过程，让turtlebot不再背着大笔记本来回跑了，但只能动不能看，这次将kinect移植到turtlebot上的A80开发板，读取视频数据传到远端pc构建地图数据。

按照ROS[官方](http://wiki.ros.org/kinect)的说法，ROS端会有不同kinect驱动:

```
- [freenect_stack](http://wiki.ros.org/freenect_stack): Newer driver using libfreenect

- [openni_kinect](http://wiki.ros.org/openni_kinect): Wraps the OpenNI "natural interaction" drivers, as well as higher level libraries like skeleton and gesture tracking. This driver is officially supported by PrimeSense, has great performance, and provides the full capabilities of the sensor, including in-sensor registration for RGB and depth (no calibration required), support for different depth and RGB resolutions, and full audio support. This driver supports Linux, OS X, and Windows.

- kinect: The ROS kinect stack wraps the libfreenect drivers. These drivers are Kinect-specific, and thus also contain access to the Kinect motors and accelerometers. This driver supports Linux and OS X.

- kinect_aux: A standalone driver for accelerometer/tilt/led extracted from the kinect driver. To run in parallel with the ni driver. 

```
### *freenect_stack驱动*

其中`freenect_stack`是基于libfreenect的kinect驱动的封装，只要安装完ROS，freenect_stack作为BareBone的一部分也会被安装，将kinect接入到A80上，使用命令：

```
> lsusb | grep Xbox
> Bus 001 Device 008: ID 045e:02ae Microsoft Corp. Xbox NUI Camera
> Bus 001 Device 006: ID 045e:02b0 Microsoft Corp. Xbox NUI Motor
> Bus 001 Device 007: ID 045e:02ad Microsoft Corp. Xbox NUI Audio

```

会发现kinect的camera、motor、audio都已正常接入。运行freenect的某一例程

```
> freenect-glview
> Kinect camera test
> Number of devices found: 1
> GL thread
> freeglut (freenect-glview): failed to open display ''
```

说明找到kinect但缺少openGL而无法显示。A80板上现在运行是lubuntu-server系统，没有跑X11环境，待稍后试验下A80 64核的GPU运行OpenGL效果如何。

### *Openni驱动*

首先安装依赖包：

```
> sudo apt-get install g++ libusb-1.0-0-dev freeglut3-dev openjdk-7-jdk doxygen graphviz mono-complete
```

安装完毕开始源码编译安装OpenNI，由于openni已被苹果公司收购，openni官网被链接到苹果官网，OpenNI只能从[github上下载](https://github.com/OpenNI/OpenNI)。按安装说明需要下载`unstable`分支。

```
> git clone -b unstable https://github.com/OpenNI/OpenNI.git
> cd OpenNI/Platform/Linux/CreateRedist
# 该目录下会有多个RedistMaker*文件，任意运行一个即可，会自动检测平台架构。
# RedistMaker可自动执行make功能，也可到`../Build`目录下手动`make`
> chmod +x RedistMaker
> ./RedistMaker
```

执行该命令会遇到很多错误，依次解决。

#### *Error 1*

出现如下字样的大段错误：

```
fatal error: sys/cdefs.h: No such file or directory
.....

```

查找原因是因为在32bit机器上编译64bit文件导致的，但A80并不是64bit，总之还是因为缺少库文件，查找`cdefs.h`文件需要安装依赖包：

```
> sudo apt-get install gcc-multilib g++-multilib
```

#### *Error 2*

继续编译会报出如下大段错误：

```
usr/bin/ld: error: ../../Bin/Arm-Release/libOpenNI.so uses VFP register arguments, ./Arm-Release/XnBaseNode.o does not
......

```

通过查找有人给出如下解决方法：

```
You're probably compiling on a linaro version newer than 12.x. Linaro had switched to hard-floats. Try removing the "-mfloat-abi=softfp" flag from Platform/Linux/Build/Common/Platform.ARM file.

在OpenNI/Platform/Linux/Build/Common/Platform.ARM文件中注释掉"-mfloat-abi=softfp"选项即可。
```

完成后继续编译，成功编译完成，之后会在`Linux`目录下生成`Redist`目录，并通过install.sh安装必要的库和可执行文件。

```
> cd OpenNI/Platform/Linux/Redist/OpenNI-Bin-Dev-Linux-Arm-v1.5.8.5
> sudo ./install.sh
```

至此`OpenNI`编译安装完成。


#### *安装SensorKinect*

接下来安装PrimeSense传感器驱动，在源码托管在[github下](https://github.com/avin2/SensorKinect)，同样需要`unstable`分支的代码。

```
> git clone -b unstable https://github.com/avin2/SensorKinect
> cd SensorKinect/Platform/Linux/CreateRedist
> chmod +x RedistMaker
> ./RedistMaker
```

编译过程与编译`OpenNI`类似，同样在这一步会出问题。

```
In file included from /usr/include/ni/XnTypes.h:28:0,
                 from /usr/include/ni/XnModuleInterface.h:27,
                 from /usr/include/ni/XnModuleCppInterface.h:33,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorProductionNode.h:28,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorGenerator.h:28,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorMapGenerator.h:28,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.h:28,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.cpp:25:
../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.cpp: In member function ‘virtual XnSensorGenerator* XnExportedSensorDepthGenerator::CreateGenerator(xn::Context&, xn::Device&, XnDeviceBase*, const XnChar*)’:
/usr/include/ni/XnOS.h:327:49: error: cannot allocate an object of abstract type ‘XnSensorDepthGenerator’
  #define XN_NEW(type, ...)  new type(__VA_ARGS__)
                                                 ^
../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.cpp:339:9: note: in expansion of macro ‘XN_NEW’
  return XN_NEW(XnSensorDepthGenerator, context, sensor, pSensor, strStreamName);
         ^
In file included from ../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.cpp:25:0:
../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.h:39:7: note:   because the following virtual functions are pure within ‘XnSensorDepthGenerator’:
 class XnSensorDepthGenerator : 
       ^
In file included from ../../../../Source/XnDeviceSensorV2/XnSensorProductionNode.h:28:0,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorGenerator.h:28,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorMapGenerator.h:28,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.h:28,
                 from ../../../../Source/XnDeviceSensorV2/XnSensorDepthGenerator.cpp:25:
/usr/include/ni/XnModuleCppInterface.h:220:20: note: 	virtual XnStatus xn::ModuleAlternativeViewPointInterface::GetPixelCoordinatesInViewPoint(xn::ProductionNode&, XnUInt32, XnUInt32, XnUInt32&, XnUInt32&)
   virtual XnStatus GetPixelCoordinatesInViewPoint(ProductionNode& other, XnUInt32 x, XnUInt32 y, XnUInt32& altX, XnUInt32& altY) = 0;
                    ^
make[1]: *** [x64-Release/XnSensorDepthGenerator.o] Error 1
make: *** [XnDeviceSensorV2] Error 2
make: *** Waiting for unfinished jobs....

```

查找问题后确定该版本中XnSensorDepthGenerator中虚函数GetPixelCoordinatesInViewPoint问题，在github上[ph4m](https://github.com/avin2/SensorKinect/pull/5)解决这一问题并git他的`unstable`版本，重复以上编译过程。

随后依然会遇到报错：

```
usr/bin/ld: error: ../../Bin/Arm-Release/libOpenNI.so uses VFP register arguments, ./Arm-Release/XnBaseNode.o does not
......

```

根据编译OpenNI经验在`/Platform/Linux/Build/Common/Platform.ARM`文件中注释掉`-mfloat-abi=softfp`选项即可。再次编译，成功！

之后使用OpenNI自带的试验例程测试Kinect。

```
> Sample-NiSimpleCreate

Frame 2083 Middle point is: 2312.
Frame 2084 Middle point is: 2297.
Frame 2085 Middle point is: 2312.
Frame 2086 Middle point is: 2312.
Frame 2087 Middle point is: 2312.
Frame 2088 Middle point is: 2297.
Frame 2089 Middle point is: 2312.
Frame 2090 Middle point is: 2327.
Frame 2091 Middle point is: 2312.
Frame 2092 Middle point is: 2312.
Frame 2093 Middle point is: 2327.
Frame 2094 Middle point is: 2312.
Frame 2095 Middle point is: 2327.
Frame 2096 Middle point is: 2312.
Frame 2097 Middle point is: 2327.
Frame 2098 Middle point is: 2312.
Frame 2099 Middle point is: 2327.
Frame 2100 Middle point is: 2312.
Frame 2101 Middle point is: 2327.
Frame 2102 Middle point is: 2297.
Frame 2103 Middle point is: 2327.
Frame 2104 Middle point is: 2297.
......
```

但若使用`Sample-NiSimpleRead`测试时，会出现错误：

```
Warning: USB events thread - failed to set priority. This might cause loss of data...
Open failed: Failed to receive a USB control request!
```

经查询是由于USB3.0的原因导致，但A80的4个U口均为2.0，并且`Sample-NiSimpleCreate`测试已经可以获取数据，需要深入对比源代码比较。



