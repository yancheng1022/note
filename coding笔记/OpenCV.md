---
title: OpenCV
date: 2024/08/30
categories:
  - coding
tags:
  - 编程基础
  - ai
  - 图像处理
  - opencv
---
# 1、基本概念
## 1.1、主要模块

OpenCV 是开源的计算机视觉库，包括了图像处理和计算机视觉方面很多通用的算法，其主要模块及功能如下：
⦁	Core：核心模块，包括基础数据结构、绘图函数、数组操作函数等；
⦁	Imgproc：图像处理模块，包括图像滤波、几何变换、直方图、形态学处理、边缘检测、特征及目标检测等；
⦁	Highgui：顶层GUI模块，包括用户界面、读/写图像及视频等；
⦁	Video：视频模块，包括运动分析及目标跟踪；
⦁	其他模块，包括Calib3d，Features2d，Objdetect，MI，Flann，Gpu，Photo，Stitching等；

## 1.2、数字图像的本质：矩阵

⦁	OpenCV中的Mat类（Matrix矩阵）
⦁	Java中调用OpenCV函数需要加上模块名称，比如：显示图像：HighGui.imshow("lena", src);
⦁	读取图像：Mat src=Imgcodecs.imread("lena.jpg")；
⦁	输出图像：Imgcodecs.imwrite("lena.jpg", mat);
⦁	要创建每个点只有一个通道的简单矩阵，可用三个静态函数：zeros，eye，ones；
⦁	CV_8UC1：8位Unsigned（取值范围0-255）；Channel（通道）=1（灰度图）；
⦁	CV_8UC3：8位Unsigned；Channel=3（彩色图像）；
⦁	CV_64FC3：64位浮点数；Channel=3（彩色图像）；
⦁	通道：彩色（RGB三个通道）；有时还有alpha通道（透明度）；

## 1.3、图形的绘制

⦁	图形的绘制：OpenCV中图形绘制只是辅助，因此不详细介绍；主要函数有：
⦁	绘制线：Imgproc.line( ); 
⦁	绘制矩形：Imgproc.rectangle( );
⦁	绘制圆：Imgproc.circle( );

```java
public class Learn {  
  
    public static void main(String[] args) {  
        String imgPath = "D:\\code\\study\\videoAnalyzer\\videoAnalyzer\\images\\anquan.jpg";  
        // 加载opencv动态库，System.load(ClassLoader.getSystemResource("lib/opencv_java470-无用.dll").getPath());  
        nu.pattern.OpenCV.loadLocally();  
        // 读取jpg文件至src中（Mat类）  
        Mat src = Imgcodecs.imread(imgPath);  
        // 在屏幕上显示图片  
        HighGui.imshow("img",src);  
        // 等待毫秒数，0表示任意键退出  
        HighGui.waitKey(0);  
    }  
}
```

# 2、图像处理基础

⦁	图像的加法：Core.add(Mat src1, Mat src2, Mat dst)；
⦁	图像的混合：Core.addWeighted(Mat src1, double alpha, Mat src2, double beta, double gamma, Mat dst)；
⦁	图像的反相：Core.bitwise_not(Mat src, Mat dst)；(底片效果)
⦁	图像的平移：将图像的所有像素点按照给定的平移量（x方向，y方向）移动；
⦁	旋转：Imgproc.warpAffine(Mat src, Mat dst, Mat M, Size dsize, int flags);
⦁	缩放：Imgproc.resize(Mat src, Mat dst, Size dsize);

```java
public static void draw(){  
    // 加载opencv动态库，System.load(ClassLoader.getSystemResource("lib/opencv_java470-无用.dll").getPath());  
    nu.pattern.OpenCV.loadLocally();  
    // 读取jpg文件至src中（Mat类）  
    String imgPath1 = "D:\\code\\study\\videoAnalyzer\\videoAnalyzer\\images\\bus.jpg";  
    String imgPath2 = "D:\\code\\study\\videoAnalyzer\\videoAnalyzer\\images\\anquan.jpg";  
    Mat src1 = Imgcodecs.imread(imgPath1);  
    Mat src2 = Imgcodecs.imread(imgPath2);  
    Mat des = new Mat();  
    Core.bitwise_not(src1, des);  
    HighGui.imshow("img", des);  
    HighGui.waitKey(0);  
}
```

⦁	图像的仿射变换
	概念：图1中的点1，2，3与图2中的点1，2，3一一对应，利用这3组对应点求出两者的变换矩阵，然后把它应用到整幅图像；
	3点构成一个面；仿射变换的变换矩阵大小为2*3；
	主要函数：

```java
	Imgproc.getAffineTransform(MatOfPoint2f src, MatOfPoint2f dst)；//2*3的变换矩阵
	Imgproc.warpAffine(Mat src, Mat dst, Mat M, Size dsize)；//应用变换矩阵变换图像
```

⦁	透视变换
	概念：透视变换(Perspective Transformation)将图片投影到一个新的视平面，也称作投影映射；要找到4个点，其中任意三个不共线；透视变换矩阵M大小为3*3；
	主要函数：

```java
Imgproc.getPerspectiveTransform(Mat src, Mat dst)；
Imgproc.warpPerspective(Mat src, Mat dst, Mat M, Size dsize, int flags)；
```