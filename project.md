

## 任务书原文内容：

(1)基于3D视觉系统机器人修坯轨迹生成技术

​		基于三角成像原理和3D视觉系统,开发工件表面点位三维点云数据采集系统,提出基于数据高速采集系统 三维模型重构与局部特征识别 模型工艺分解的在线修坯轨迹生成技术

(2)基于3D视觉的融合主恒动力装置的局部轨迹修整技术

​	    针对不同批次的陶瓷素坯不一致及修坯过程磨料磨损问题,采用恒动力装置的力反馈技术实时对修坯轨迹进行局部修整 降低现场工人对机器人的示教及离线编程技术的要求,并基于**结构光高速扫描技术与精确配准算法**的工件三维重构方法和在线三维局部特征目标识别技术,实现融合多种技术的局部轨迹修整体系;

![image-20200909190217955](/home/zhangeaky/.config/Typora/typora-user-images/image-20200909190217955.png)

## 思路和想法

### 一、搭建3D视觉数据采集系统:

> 采集素坯的点云数据,为后续处理做准备

1. 3D视觉系统传感器选型

   ```mysql
   1.双目相机: 如 ZED2 
   	原理：三角测量原理，左右图像配准，立体匹配；受环境光照影响比较大
   2.Time Of Flight 相机
   	原理：主动投出激光脉冲，使用时间和速度计算距离
   3.结构光相机: Microsoft kinect v2
   	原理：由相机和编码的结构光发射器组成，类似于三角测量原理，可分为条纹,编码,散斑结构光
   4.融合模式深度相机： Intel RealSense D400 Series
   	原理：结合了双目三角测距原理和红外结构光主动投射两种方式，能够克服一些双木相机不能解决的问题
   ```
   

   

![img](https://img2018.cnblogs.com/blog/976394/201907/976394-20190715220139011-2055005643.jpg)



2. 如何**精确标定**抛磨机器人的3D视觉系统

   ```
   1.内参标定
   2.外参标定:眼在手上,眼在手外标定
   ros:Aruco easyhandeye 功能包支持
   ```

   *参考来源：*

   - https://zhuanlan.zhihu.com/p/92339362

### 二、点云数据获取与处理

> - 获取加工目标素坯的几个视角的点云帧
> - 为三位重建创造前提

1. 点云噪声分析

   ```mysql
   1）离群点，设备测量误差造成,会使点云的局部特征复杂化
   2）边缘噪声，边缘深度突变
   3）不稳定的点云稀疏
   ```

2. 点云预处理

   ```mysql
   降噪处理：
   1）自适应RadiusOutlierRemover滤波器
   2）统计滤波器
   ```

*参考来源：*

- 基于Kinect的物体三维模型构建和姿态估计方法研究

3. 点云分割（背景去除，平面分割）

   ```mysql
   目的：从场景点云中提取目标的素坯点云堆
   方法：
   	1）基于边缘的区域分割
   	2）基于面的区域分割
   	3）基于聚类的区域分割
   	4）混合区域分割方法
   	5）深度学习方法
   	6）随机采样一致性Ransac算法
    (区域声场、Ransac线面提取、NDT-RANSAC、K-Means（谱聚类）、Normalize Cut、3D Hough Transform(线面提     取)、连通分析）
     
     JSNet，SegMatch，PASS3D，PointNet
     
     
     
     
    
   ```

   
   
   ```

   ```

   

   

   

   4.多视角点云配准
   
   ```C++ 
   目的：建立素坯的点云三维模型
   因为目标是陶瓷工件并且由于工件的集合复杂性和构造的特属性，需要使用多相机对工件的表面特征进行采集并进行配准融合。
   基于旋转平台方案：
   //相机视角A
   	设置好旋转平台的旋转角度 采集好初始位姿的点云数据 旋转角度x，2x ...
   并根据角度将点云乘上旋转矩阵进行粗配准，然后使用算法进行精确配准
   //相机视角B
       标定好相机A和相机B之间的旋转关系 利用旋转关系进行来两个相机之间点云的粗配准
   
   ```
   
   5.点云重采样
   
   ```
   
   ```

参考来源：*

- https://github.com/Tom-Hardy-3D-Vision-Workshop/awesome-pointcloud-processing
- 基于kinect的物体三维模型构建和姿态估计方法研究

### 三、 三维重建、曲面重建

> 针对已经配准过的点云模型数据进行三维曲面重建
>
> 为了能够精确对物体进行形状分析，并与样本模型配准，以及进行后续加工点分析

```mysql
方法：
	1）泊松算法、Delauary triangulatoins(Delauary三角化)
	2）贪婪投影三脚化算法
    3）移动立方体
```

理想的三维重建效果：

![image-20200909191451312](/home/zhangeaky/.config/Typora/typora-user-images/image-20200909191451312.png)

*参考来源：*

- State of Art on 3D Reconstruction with RGB-D cameras
- 改进的点云数据三维重建算法

### 四、抛磨任务与轨迹生成

1. 从**待加工工件**表面获取加工信息,以引导抛磨工具进行抛磨作业,对比**样本工件**点云(合格工件的点云样本)和待加工的测试点云,进行精确的点云对配准,进一步分析提取加工轨迹

   ```mysql
   点云配准:
   	1）正态分布变换和IPC点云配准
   	2）ICP：稳健ICP、point to plane ICP、point to line ICP、MBICP、GICPNDT 3D、Multil-Layer NDT
   	FPCS、KFPSC、SAC-IA（采样一致性初始配准方法）
   	3）Line Segment Matching、ICL
   	
   搜索差异点云：FLANN 算法
   ```

2. 通过分割**差异点云**进行**加工路径**提取

   ```
   1.寻找差异点云的最小包围盒
   2.包围盒分割为立方体
   3.计算立方体内的点云质心
   4.使用紧邻搜索找到测试点云上距离质心最近的点作为加工路径点
   ```
   
   *参考来源：*
   
   - 陶瓷素坯抛磨机器人的三维加工目标检测与运动规划研究_刁世普

3. 多加工目标点加工先后顺序决策

   ```mysql
   1. 目标函数:时间最短,路径最短...
   ```

### 五、局部轨迹修整

1. 结构光高速扫描

   ```mysql
   1）在机械臂加工的过程中
   2）结构光在近距离的距离测量精度非常高
   ```

2. 局部特征识别与轨迹修整合

   ```
   
   ```

### 六、可能存在的碰撞轨迹规划

1. 碰撞判断？

   ```
   按照上述方法决定各个加工点加工顺序后,机械臂按该顺序一次进行抛磨作业，因此：
   	若对机器人的末端来说相邻的两个作业目标点之间的工作姿态变化较大,可能在变化的过程当中产生碰撞问题，
   在加工路径中检测碰撞物体,并规划从一个加工点到另一个加工点的避障路径。
   ```

2.   moveit！避障检测

   ```
   建立octocmap，判断障碍物，规划出避障路径
   ```

*参考来源：*

点云处理: https://github.com/Tom-Hardy-3D-Vision-Workshop/awesome-pointcloud-processing

### 七、末端与打磨使用规则

1. 根据素坯的人工打磨规则进行机器人编程
2. 根据打磨加工的位置选择软硬和大小不同的打磨头
3. 根据表面的曲率确定机器人的末端姿态

### 八、 工型号匹配

```
不同的型号的马桶素坯工件具有不同的样本模型，根据扫描的素坯模型确定好匹配的样本的模型
```





